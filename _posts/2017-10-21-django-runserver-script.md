---
layout: post
title:  Django migrate, runserver 쉘 스크립트 작성하기
tags:   ['Django', 'Script']
---

> 자주 사용하는 명령어들을 쉘 스크립트를 만들어서 사용하면 편리하다. Django를 이용하면서 `python manage.py migrate`, `python manage.py runserver` 등의 반복적으로 사용하는 명령어들을 하나의 스크립트로 작성해서 사용하고 있다.  

_myrunserver.sh_  
```bash
#!/bin/sh

PROJECT_DIR=$(cd "$(dirname "$0")" && pwd)
DB_FILE=$PROJECT_DIR/db.sqlite3

if [ -f "$DB_FILE" ]; then
    rm $DB_FILE
fi

cd $PROJECT_DIR
python3 manage.py migrate
python3 manage.py runserver
```  

<br/>  

## dirname $0  

### dirname  

`$ dirname 경로 or 경로 + 파일` 형태로 사용하고 결과로는 디렉토리의 경로가 반환된다. 입력에 따라 상대경로 이면 상대경로를, 절대경로이면 절대경로를 반환한다.  

```
$ dirname /jihun/test.sh
/jihun
$ dirname /jihun/testdir
/jihun
$ dirname ../test/test.sh
../test
```

### $0  

명령 라인이 아닌 쉘 스크립트 파일에서 실행될 경우 경로를 포함한 파일명을 반환한다.   

```
$ echo 'echo $0' > test.sh
$ sh ./test.sh
./test.sh
$ sh /jihun/test.sh
/jihun/test.sh
```  

### 실행된 스크립트 경로 구하기   

`PROJECT_DIR=$(cd "$(dirname "$0")" && pwd)` 와 같이 이용하면 어떤 경로에서 스크립트를 실행 하는지에 상관없이 실행된 스크립트 파일의 절대 경로를 알아낼 수 있다.  

`dirname "$0"` 명령으로 알아낸 쉘 스크립트 파일 위치로 이동한 후 `pwd` 명령으로 경로를 얻고, 변수에 저장하여 사용한다.  
