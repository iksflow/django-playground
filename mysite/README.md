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

## 3. Database 설치
기본적으로 SQLite가 설치되어있지만 production 운영환경을 구성하는 연습을 위해 PostgreSQL으로 설정해보았다.

### 3.1. psycopg 설치
Python의 PostgreSQL 클라이언트 의존성인 psycopg를 설치한다.
```sh
$ pip install psycopg
```
### 3.2. postgresql 설치
docker compose를 활용해 PostgreSQL를 설치한다. 자세한 설정 내용은 docker-compose.yml을 참고.
```sh
$ docker compose up
```
참고 - [Docker Hub - Postgres](https://hub.docker.com/_/postgres)
### 3.3. PostgreSQL 설정 정보 입력
`settings.py`의 DATABASES에 DB 정보를 입력한다.

```python
#...기타 코드 생략...
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": "mysite",
        "USER": "devuser",
        "PASSWORD": "password",
        "HOST": "localhost",
        "PORT": 6432,
    }
}
```
참고 - [Django DB 설정](https://docs.djangoproject.com/ko/4.2/ref/settings/#std-setting-HOST)
### 3.4. migrate 실행
프로젝트 생성 후 django에서 제공하는 기본 설정 테이블의 migrate를 진행하지 않았다.  
아래 명령어를 실행해서 migrate한다.
```sh
$ python manage.py migrate
```

## 4. 패키지 설정 내보내기
pip install로 가상환경에 설치한 패키지 정보를 requirements.txt에 저장한다.  
```sh
$ pip freeze > requirements.txt
```
