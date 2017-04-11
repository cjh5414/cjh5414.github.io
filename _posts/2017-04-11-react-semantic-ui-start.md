---
layout: post
title:  Semantic UI React 시작하기
tags:   [JavaScript, React, Semantic UI]
---

React에서 Semantic UI를 적용할 때 [Semantic UI React](http://react.semantic-ui.com/introduction)를 이용하면  

```html
<div
  class="ui rating"
  data-rating="1"
  data-max-rating="3"
></div>
```  

와 같이 사용하지 않고  

```html
<Rating rating={1} maxRating={5} />
```  

처럼 React Component로 사용할 수 있다.  
또한 `Semantic UI React`는 jQuery 를 사용하지 않는다.  

<br/>  

### Semantic UI React Install  

```
$ npm install --save semantic-ui-react semantic-ui-css
```  

<br/>   

## Usage  

우선, src/index.js 파일에서 Semantic-UI의 CSS 파일을 불러와야한다.  

```javascript
import 'semantic-ui-css/semantic.min.css';
```  

그 다음, [Semantic UI React](http://react.semantic-ui.com/introduction) 문서를 참고하여 원하는 스타일을 지정하여 사용하면 된다.  

```javascript
import { Container } from 'semantic-ui-react'

import React from 'react'
import { Container, Header, Button } from 'semantic-ui-react'

const Example = () => (
  <Container text>
    <Header as='h2'>Header</Header>
    <p>text</p>
    <Button>button</Button>
  </Container>
)

export default ContainerExampleText
```  
