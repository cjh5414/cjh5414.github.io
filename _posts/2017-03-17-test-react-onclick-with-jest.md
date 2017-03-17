---
layout: post
title:  Jest로 React onClick function 테스트 하기
tags:   [React, Jest, JavaScript]
---

> [Jest](https://facebook.github.io/jest/), [Enzyme](http://airbnb.io/enzyme/index.html), [Sinon.js](http://sinonjs.org/)를 이용하여 React에서 특정 component의 onClick 기능이 잘 동작 하는지를 테스트하는 방법에 대해서 알아보았다.  

<br/>  

Enzyme의 Full DOM Rendering 테스트 코드의 예시 중 하나이다.  

```javascript
import { mount } from 'enzyme';
import sinon from 'sinon';
import Foo from './Foo';

describe('<Foo />', () => {

  [...]

  it('simulates click events', () => {
    const onButtonClick = sinon.spy();
    const wrapper = mount(
      <Foo onButtonClick={onButtonClick} />
    );
    wrapper.find('button').simulate('click');
    expect(onButtonClick.calledOnce).toEqual(true);
  });
});
```  

### Sinon.js   

예제를 보면 [Sinon.js](http://sinonjs.org/) 을 사용하고 있는데 JavaScript에서 mock 기능을 이용할 수 있는 라이브러리이다.  

`const onButtonClick = sinon.spy();` onButtonClick 이라는 상수형 변수에 mock 함수를 만들어서 지정하였다.  

### Enzyme Full Rendering  

[Enzyme](http://airbnb.io/enzyme/index.html)에는 `Shallow`, `Full`, `Static` 의 3가지 rendering 방식이 있다.

그 중에서 `Full DOM Rendering API`의 `mount()` 함수를 이용하여 `Foo` Component의 rendering 결과를 `wrapper` 변수에 저장한다. 이 때, sinon으로 생성했던 mock 함수인 {onButtonClick}을 `onButtonClick` selector로 지정하여 rendering 한다.  

`bind()`, `simulate()` 함수를 이용하여 rendering 결과에서 `<Button>` 태그 혹은 관련 selector을 지정하여 클릭하는 행위를 한다. (위의 예제에서는 Button 태그를 사용하는 것을 테스트)

그 다음, expect() 함수를 이용하여 mock으로 지정했던 함수의 `onButtonClick.calledOnce` 의 값을 가지고 참, 거짓을 테스트 한다. clickable 하면 mock 함수가 호출되어 `calledOnce` 값이 참이 되므로 테스트가 통과할 것이고 그렇지 않으면 실패할 것이다.  

<br/>  

## onClick에 등록된 함수 테스트하는 방법  

특정 component의 onClick에 등록된 함수가 제대로 동작하는지를 테스트 하고 싶다면 mock 함수를 지정하지 않고 `simulate()` 만 이용해서 할 수 있다.  

예를 들어, `Foo` component에 `num`이란 state가 0으로 초기화 되어있고 `num` 값을 1씩 증가시키는 기능의 numPlus() 라는 함수를 `button`에 onClick() 함수로 등록해놓고 이것을 테스트하는 코드를 작성한다고 가정한다.  

```javascript
import { mount } from 'enzyme';
import Foo from './Foo';

describe('<Foo />', () => {
  it('simulates click events', () => {
    [...]
    const wrapper = mount(
      <Foo />
    );
    expect(wrapper.state('num')).toEqual(0);
    wrapper.find('button').simulate('click');
    expect(wrapper.state('num')).toEqual(1);
  });
});
```   

버튼을 클릭 하기 전에 `num`의 값은 0으로 설정돼 있을 것으므로 이 내용을 `expect()`으로 작성하고, `simulate()` 함수를 통해서 `button`을 클릭하면 `num`을 증가시키는 함수에 의해 `num` 값이 1로 증가하는 것을 확인하는 테스트 코드를 작성할 수 있다.  
