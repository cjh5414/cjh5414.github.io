---
layout: post
title:  Markdown Editor for Mac
tags:   [Markdown]
---

<br>  

> mac에서 무료인 Markdown Editor를 몇 가지 사용해보고 장단점을 비교해봤다.    
MacDown, Sublime Text 3, Atom을 사용해봤는데 가장 마음에 들고 추천하고 싶은 Editor는 Atom이다.  
프로젝트 폴더 관리 기능을 제공하기 때문에 문서 작업을 하면서 동시에 이미지 파일을 저장하던지, 새로운 파일을 추가한다던지 등의 파일 관리를 할 수 있고 무엇보다 2000개가 넘는 패키지를 지원하고 있어서 매우 다양한 기능들을 사용할 수 있는 장점이 있다.

## MacDown  

### 설치  

[MacDown 다운로드](<http://macdown.uranusjr.com/>) 에 들어가서 페이지 하단의 버튼을 클릭하면 된다. 다운받은 앱을 실행 시켜보면 다음과 같은 오류 메세지가 나온다.  

![error](/images/md-editor/error.png)  

시스템 환경설정 -> 보안 및 개인 정보 보호 -> 일반  

자물쇠 모양을 클릭하고 다음에서 다운로드한 App 허용을 '모든 곳'으로 바꿔준다  

![setting](/images/md-editor/setting.png)  

### 사용   

![macdown](/images/md-editor/use.png)  

좌측 검은색 부분에 텍스트를 입력하면 우측 하얀부분에 마크다운이 적용된 결과가 바로 출력된다. 입력 부분에도 마크다운 문법에 따라 텍스트의 색이 다르게 표시된다.  
마우스  우클릭을 하면 맞춤법검사, 읽어주기, 단어검색 등의 추가적인 기능이 있다.  

### 사용 후기  

디자인이 매우 깔끔하다. 입력하는 내용을 바로바로 볼 수 있어서 편리하다.
파일 목록을 보여주는 기능이 없어서 다소 불편하다.  


## Sublime Text 3

### 설치 & 사용

[Sublime Text3 다운로드](https://www.sublimetext.com/3) 에 들어가서 운영체제에 맞는 버젼을 다운받는다.  

마크다운을 조금 더 편리하게 사용하기 위해 플러그인을 설치한다.  

Sublime Text를 실행하고 단축키 Ctrl + \` 을 입력한 후 아래 내용을 복사해서 붙여넣는다. ( <https://packagecontrol.io/installation#st3> 참고 )   

```
import urllib.request,os,hashlib; h = 'df21e130d211cfc94d9b0905775a7c0f' + '1e3d39e33b79698005270310898eea76'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```   

단축키 `Ctrl+Shift+P`을 입력하면 창이 뜬다. `install`을 입력하여 자동완성 되는 `Package Control: Install Package`을 클릭한다.  

`Markdown`을 입력하여 원하는 플러그인을 설치한다.  

여기서는 Markdown Editing과 Markdown preview 를 사용했다.  

Markdown Editing을 설치한 후에 sublime을 다시 시작하고 새로운 파일을 만든다. 우측 하단에 확장자를 클릭하여 Markdown Editing을 클릭한다.    

![Sublime Markdown Editing](/images/md-editor/sublime-md-editing.png)  

Markdown preview는 설치 후에 설정이 필요하다. 단축키 `command + ,` 를 입력하여 설정 파일을 열고 아래의 텍스트를 입력하고 저장한다.

```
{ "keys": ["alt+m"], "command": "markdown_preview", "args": {"target": "browser", "parser":"markdown"} }
```  

다시 Markdown Editing으로 돌아와서 단축키 `Ctrl+Shift+P`를 입력한다. 검색창에 'Markdown Preview'를 입력하면 자동완성되는 "Markdown Preview: Python Markdown: Preview in Browser"를 실행한다.  

![Sublime Markdown Editing](/images/md-editor/sublime-md-preview.png)  


### 사용 후기  

우선 플러그인 설치부터 사용하기까지 너무 번거롭고 불편하다. 블로그 작성 초기에 markdown을 사용하기 위해 이미 설치되어 있는 sublime text로 확장자만 변경하여 사용했었는데 이 때 불편함을 많이 느껴서 markdown을 찾아보는 계기가 되었다. sublime text를 이용하여 markdown을 사용하는 것은 추천하지 않는다.


## Atom  

### 설치  

[Atom 다운로드](https://atom.io/) 에서 다운받을 수 있다.  

### 사용  

![Atom use](/images/md-editor/atom-use.png)  

내용을 입력하고 `Ctrl+Shift+m`단축키를 입력하면 좌측에 결과가 출력된다.

settings`command+,` 에 들어가서 core settings, editor settings, keybindings, themes 등을 설정하면 편리한 기능들을 더 많이 사용할 수 있다. 특히, 2000개나 넘는 packages가 존재하고 있어서 필요한 패키지를 설치하여 유용하게 사용할 수 있다.  

settings의 사용법을 자세히 설명해 놓은 동영상이 있다.  
<https://www.youtube.com/watch?v=U5POoGSrtGg>  


### 사용 후기

개인적으로 가장 마음에 드는 에디터이다. 기능이 많고 내가 원하던 기능을 갖추고 있어서 애용하고 있다. 블로그 포스팅에 사용하는 경우에는 새로운 포스트를 추가하거나 이미지를 올리는 등의 파일을 관리할 필요가 있는데, atom은 글을 작성하면서 동시에 프로젝트 폴더를 관리도 할 수 있어서 매우 편리하다. git-plus라는 패키지를 설치해서 terminal에서 git 명령을 치거나 git 관련 프로그램을 사용하지 않고 단축키로 간편하게 git을 사용하고 있다.
