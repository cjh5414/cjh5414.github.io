---
layout: post
title:  예제를 통한 Redux 이해하기
tags:   [React, Redux, JavaScript]
---

## Redux Example  

Redux를 이해하기 위해 사용되는 예제는 Inflearn의 김민준(velopert)님이 강의하신 [React & Express를 이용한 웹 어플리케이션 개발하기](https://www.inflearn.com/course/react-%EA%B0%95%EC%A2%8C-velopert/) 의 Redux 강좌의 예제를 사용하였다. 전체 소스코드는 [redux-example](https://github.com/cjh5414/redux-example)에 올라가 있다.  

![app result](/images/redux-example/app result.png)  

다음과 같은 3개의 버튼이 있다. `+`, `-` 버튼은 위의 숫자를 증가, 감소 시키고, `Randomize Color` 버튼은 배경의 색을 랜덤하게 바꿔준다.  

<br/>  

## App Setup  

### create App

[Create React App](https://github.com/facebookincubator/create-react-app)을 이용하여 App을 생성한다.  

```
$ create-react-app redux-example
$ cd redux-example/
$ npm start
```   

필요없는 파일들을 제거해준다. 어플리케이션 결과물의 directory 구조는 아래와 같이 구성된다.  

![dir structure](/images/redux-example/dir structure.png)  

<br/>  

## Redux Example   

### 1. 액션과 액션 생성자   

애플리케이션의 상태를 바꾸려면 항상 액션을 보내야만 한다. 이것이 상태를 바꿀 수 있는 유일한 방법이다. 뷰에서 액션을 요청하면 액션 생성자가 포맷을 변경하여 돌려주고 이 액션을 통해 상태를 바꾸게 된다.

__액션 정의__  

_src/actions/ActionTypes.js_  

```javascript
export const INCREMENT = "INCREMENT";
export const DECREMENT = "DECREMENT";
export const SET_COLOR = "SET_COLOR";
```  

__액션 생성자__  

_src/actions/index.js_  

```javascript
import * as types from './ActionTypes';

export function increment() {
    return {
        type: types.INCREMENT
    };
}

export function decrement() {
    return {
        type: types.DECREMENT
    };
}

export function setColor(color) {
    return {
        type: types.SET_COLOR,
        color // color: color와 동일
    };
}
```  

### 2. 스토어 생성 및 리듀서 지정  

Redux에서는 단 하나의 __Store__ 가 상태 트리 전체를 유지하는 책임을 진다. 어떤 액션이 들어왔을 때 필요한 상태 변화에 대한 일을 __Reducers__ 에게 위임한다. __Store__ 에 __Reducers__ 를 지정해주기 위해 루트 컴포넌트에서 `createStore()` 함수를 이용한다.  

_src/index.js_  

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

import App from './components/App';

import { createStore } from 'redux';
import reducers from './reducers';

import { Provider } from 'react-redux';

const store = createStore(reducers);

ReactDOM.render(
    <Provider store={store}>
        <App/>
    </Provider>,
    document.getElementById('root')
);
```   

__Store__ 를 생성하고, Root Component `<App/>`에 `<Provider/>` Component로 감싸면서 __Store__ 를 지정한다.  

__Reducers__ 는 트리 구조로 이루어져있으며 `combineReducers()`를 이용하여 다수의 리듀서를 하나로 묶는다.  _src/reducers_ 에 위치한 _.js_ 파일들이 reducer 이며 _src/reducers/index.js_ 에서 각각의 Reducer를 import 하고 하나로 묶어주는 작업을 한다.  

Store은 상태 트리와 액션을 Reducer에게 넘겨주어 처리하도록 한다.  
Reducer는 받은 상태조각을 변경하여 넘겨주지 않고 그 복사본을 변경하여 돌려준다.  

_src/reducers/index.js_  

```javascript
import { combineReducers } from 'redux';
import counter from './counter';
import ui from './ui';

export default const reducers = combineReducers({
    counter, ui
});
```  

_src/reducers/counter.js_  

```javascript
import * as types from '../actions/ActionTypes';

const initialState = {
    number: 0
};

export default function counter(state = initialState, action) {

    switch(action.type) {
        case types.INCREMENT:
            return { ...state, number: state.number + 1 };
        case types.DECREMENT:
            return { ...state, number: state.number - 1 };
        default:
            return state;
    }
}
```  

_src/reducers/ui.js_  

```javascript
import * as types from '../actions/ActionTypes';

const initialState = {
    color: [255, 255, 255]
};

export default function ui(state = initialState, action) {
    if(action.type == types.SET_COLOR) {
        return {
            ...state,
            color: action.color
        };
    } else {
        return state;
    }
}
```  

### 3. 스토어와 컴포넌트 연결  

__Component__ 들은 트리 계층구조로 이루어져 있다. 그 중에 가장 위에 위치하는 Component를 Root Component(루트 컴포넌트)라고 하며 예제에서는 `<App/>`이다. Root Component는 애플리케이션을 초기화하는 몇 가지 작업을 한 후에는 거의 하는일이 없는데, 위에서 봤듯이 Reducer를 연결하여 Store를 생성하는 것 외에도 `View layer binding`과 `View`를 불러오는 일을 한다.  

`react-redux`가 View layer binding(뷰 레이어 바인딩)에 해당한다.  `connect()` 를 이용하여 Root Component 밑의 Component들이 Store에 연결되기 쉽게 만들어준다. `connect()`의 파라미터로 `mapStateToProps`와 `mapDispatchProps`를 전달하여 하위의 `props`를 지정할 수 있다.  

_/src/components/Counter.js_  

```javascript
import React, { Component, PropTypes } from 'react';

import Value from './Value';
import Control from './Control';
import { connect } from 'react-redux';

import * as actions from '../actions';

class Counter extends Component {
    constructor(props) {
        super(props);
        this.setRandomColor = this.setRandomColor.bind(this);
    }

    setRandomColor() {
        const color = [
            Math.floor((Math.random()*55) + 200),
            Math.floor((Math.random()*55) + 200),
            Math.floor((Math.random()*55) + 200)
        ];

        this.props.handleSetColor(color);
    }

    render() {
        const color = this.props.color;
        const style = {
            background: `rgb(${color[0]}, ${color[1]}, ${color[2]})`
        };

        return (
            <div style={style}>
                <Value number={this.props.number}/>
                <Control
                    onPlus={this.props.handleIncrement}
                    onSubtract={this.props.handleDecrement}
                    onRandomizeColor={this.setRandomColor}
                />
            </div>
        );
    }
}

const mapStateToProps = (state) => {
    return {
        number: state.counter.number,
        color: state.ui.color
    };
};

const mapDispatchProps = (dispatch) => {
    return {
        handleIncrement: () => { dispatch(actions.increment())},
        handleDecrement: () => { dispatch(actions.decrement())},
        handleSetColor: (color) => { dispatch(actions.setColor(color))}
    };
};

export default connect(mapStateToProps, mapDispatchProps)(Counter);
```  

이렇게 상위 컴포넌트인 `<Counter />`에 connect을 해주면 하위 컴포넌트들인 `<Control />`, `<Value />` 에서 지정된 `mapStateToProps`, `mapDispatchProps`을 props로 접근할 수 있다.  

Redux의 컴포넌트는 똑똑한 컴포넌트(smart components)와 멍청한 컴포넌트(dumb component)로 이루어져있다. 예제에서는 `<Counter />`가 똑똑한 컴포넌트, `<Control />`, `<Value />` 가 멍청한 컴포넌트에 해당한다. 멍청한 컴포넌트는 액션 처리를 책임진다. 컴포넌트에서 액션을 보낼 필요가 있을 때, 똑똑한 컴포넌트는 props를 통해서 멍청한 컴포넌트에 함수를 보내고 멍청한 컴포넌트는 단순히 호출만 한다. 똑똑한 컴포넌트는 자기 자신의 CSS style, DOM을 거의 가지고 있지 않고, 멍청한 컴포넌트들을 관리한다.  

_src/components/App.js_  

```javascript
import React, { Component } from 'react';
import Counter from './Counter';

export default class App extends Component {
    constructor(props) {
        super(props);
    }

    render() {
        return (
            <Counter />
        );
    }
}
```  

_src/components/Control.js_  

```javascript
import React, { Component, PropTypes } from 'react';

const propTypes = {
    onPlus: PropTypes.func,
    onSubtract: PropTypes.func,
    onRandomizeColor: PropTypes.func
};

function createWarning(funcName) {
    return () => console.warn(funcName + ' is not defined');
}

const defaultProps = {
    onPlus: createWarning('onPlus'),
    onSubtract: createWarning('onSubtract'),
    onRandomizeColor: createWarning('onRandomizeColor')
};

export default class Control extends Component {
    constructor(props) {
        super(props);
    }

    render() {
        return (
            <div>
                <button onClick={this.props.onPlus}>+</button>
                <button onClick={this.props.onSubtract}>-</button>
                <button onClick={this.props.onRandomizeColor}>Randomize Color</button>
            </div>
        );
    }
}

Control.propTypes = propTypes;
Control.defaultProps = defaultProps;
```  

_src/components/Value.js_  

```javascript
import React, { Component, PropTypes } from 'react';

const propTypes = {
    number: PropTypes.number
};


const defaultProps = {
    number: -1
};

export default class Value extends Component {
    constructor(props) {
        super(props);
    }

    render() {
        return (
            <div>
                <h1>{this.props.number}</h1>
            </div>
        );
    }
}

Value.propTypes = propTypes;
Value.defaultProps = defaultProps;
```  

<br/>  

## 데이터 흐름  

![data flow](/images/redux-example/data flow.png)  

맨 위의 결과 화면에서 `+` 버튼을 눌렀다고 가정해보자.   

_src/components/Control.js_ 의 `onPlus`에 의해 _sr/components/Counter.js_ 의 `mapDispatchProps`로 지정했던 `handleIncrement`가 호출이 되고 `increment()` 액션 생성자가 plus 포맷에 맞게 변경한다.  
액션은 Store로 보내지고 Store은 상태 트리와 함께 이 액션을 Reducers로 보낸다. 값을 증가시키는 액션이므로 알맞은 Reducer인 `counter`에 number state와 함께 넘겨지고 `counter` Reducer는 상태의 복사본을 만들고 값을 1 증가시킨 후 다시 돌려준다. (이 때, color state 조각은 필요하지 않으므로 전달하지 않고 number state 조각만 주고 받는다.)  
스토어는 돌려받은 상태를 적용하여 새로운 상태트리로 바꾸고 뷰 레이어 바인딩에게 애플리케이션 상태가 변경되었음을 알린다.   
뷰 레이어 바인딩은 스토어에게 새로운 상태를 보내달라고 요청하고 받아서 뷰에게 화면을 업데이트하도록 요청한다.  

<br/>  

## 참고자료  

- <http://bestalign.github.io/2015/10/26/cartoon-intro-to-redux/>  
- https://www.inflearn.com/course/react-%EA%B0%95%EC%A2%8C-velopert/>  
