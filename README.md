# Django Playground
장고의 프로젝트 설정방법과 기능에 대해 학습.

## 1. Django Project 초기 설정
### 1.1. Python 버전 설정
```sh
$ pyenv install 3.11.3
$ pyenv local 3.11.3
```
### 1.2. virtualenv 설정
```sh
$ python -V
$ python -m venv .venv
$ source .venv/bin/activate
```

### 1.3. django 의존성 설치
```sh
$ pip install --upgrade pip
$ pip install Django
```

### 1.4. django 프로젝트 생성
```sh
$ django-admin startproject mysite
```

### 1.5. 서버 실행
```sh
# 기본값은 127.0.0.1:8000 으로 실행됨(localhost)
$ python manage.py runserver  

# host, port 설정은 아래와같이 변경 가능. e.g.) 로컬머신 외부에 8080포트로 공개하려면 아래와 같이 실행 
$ python manage.py 0.0.0.0:8080
``` 

## 2. app 생성
장고의 컨셉은 하나의 프로젝트에 여러 앱(기능)으로 나누어 관리할 수 있게 되어있다.  
앞서 `django-admin startproject {프로젝트명}` 으로 프로젝트를 생성한 것과 비슷하게 앱을 생성할 수 있다.  
앱에는 관련된 로직(route, view 등), 파일을 추가하면 된다.  
polls라는 앱을 등록하려면 아래와 같이 명령어를 입력한다.  
```sh
$ django-admin startapp polls
```
그럼 결과물로 mysite/polls 에 필요한 구조가 생성된다.