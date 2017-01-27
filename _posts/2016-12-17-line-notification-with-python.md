---
layout: post
title:  "Line Notification with python"
tags:   [Python]
---

<br>  

> Line에서 Http 호출을 이용하여 라인 메시지를 보낼 수 있는 기능을 제공한다.  Command line 과 python 을 이용하여 Line Notify를 이용하는 방법에 대해서 알아보았다.  

<br>  

## Access Token Generate  

먼저 [Line Notify](https://notify-bot.line.me/my/)에 로그인을 한 후,

하단의 `Generate token` 버튼을 클릭한다.  

![line notify](/images/line-notify/line-notify.png)  

<br>  

첫번째 빈 칸에는 Line 대화창에서 notification을 제공하는 주체의 이름을 지정한다.  

두번째 빈 칸에는 notifications을 보낼 대화창을 선택한다. 그룹이 존재하면 해당 그룹을 선택할 수 있고, 자신에게 보내고 싶을 때는 `1-on-1 chat with LINE Notify`를 선택하면 된다.  


![line notify group](/images/line-notify/line-notify-group.png)  

지정한 이름은 메시지를 받을 때 아래와 같이 보여진다.  

![line notify name](/images/line-notify/notify-name.png)  

<br>  

`Generate toekn` 버튼을 누르면 token이 생성된다. `Close` 버튼을 누르면 token 값을 다시 볼 수 없으므로 token 값을 복사하여 따로 저장해두도록 하자.

![line notify token](/images/line-notify/line-notify-token.png)  

<br>   

## Line Notify with Command line  

```
$ curl -X POST -H 'Authorization: Bearer <access_token>' -F 'message=foobar' https://notify-api.line.me/api/notify
{"status":200,"message":"ok"}   
```    

`<access_token>` 자리에 조금 전에 생성한 token 값을 넣어준다.   

'message=foobar' 부분에 notify 하고싶은 내용을 작성한다.   

<br>   

## Line Notification with python  

```python  
import sys
import requests


if len(sys.argv) != 3:
    print('error: wrong arguments')
    print('usage: python line-notify.py [ACCESS_TOKEN] [MESSAGE]')
    sys.exit()

ACCESS_TOKEN = sys.argv[1]
MESSAGE = sys.argv[2]

URL = 'https://notify-api.line.me/api/notify'
MESSAGE_FIELD = {'message' : MESSAGE}
LINE_HEADERS = {
    'Authorization': 'Bearer ' + ACCESS_TOKEN
}

try:
    response = requests.post(
        url=URL,
        headers=LINE_HEADERS,
        data=MESSAGE_FIELD
    )
    print('Response HTTP Status Code: {status_code}'.format(
        status_code=response.status_code))
except requests.exceptions.RequestException:
    print('HTTP Request failed')
```  

<br>  

### How to use  

<https://github.com/cjh5414/line-notify.git> 을 clone 한다.  

command line에 아래의 형태로 실행한다.   

[ACCESS_TOKEN]에는 저장해둔 token 값을, [MESSAGE]에는 원하는 메세지를 ' '를 붙여서 넣어주면 된다.   

```
$ python3 line-notify.py [ACCESS_TOKEN] [MESSAGE]  
```   

<br>  

## Result   

![line notify result](/images/line-notify/notify-result.png)    


<br>  

## 참고자료   

[LINE Notify API Document](https://notify-bot.line.me/doc/en/)를 보면 더 자세한 내용을 확인 할 수 있다.  
