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

## 5. 모델 Migration
장고는 migration과 model을 별도로 관리하는 Rails와는 다르게 Model을 기반으로 migration을 생성한다.  
모델 코드를 작성하거나 수정하게되면 변경된 사항을 Django에서 인지하고 변경되 사항을 기반으로 migration 파일을 생성할 수 있다.  
migration 파일을 생성하고 나면 migrate를 실행하는 절차를 거쳐서 DB에 반영한다.  

### 5.1. Model 코드 작성
`polls/models.py` 에 모델 코드를 작성한다.  
튜토리얼의 안내대로 Question, Choice 모델을 작성했다.

```python
from django.db import models


# Create your models here.
class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField("date published")


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)

```

### 5.2. INSTALLED_APP에 등록
Model 코드를 작성하고 나면 models.py가 속한 app을 settings.py의 INSTALLED_APP에 추가해야한다.  
등록하지 않으면 Django에서 변경사항을 감지하지 않기 때문에
이어서 진행할 `python manage.py makemigrations polls` 명령어를 실행했을 때 ` No migrations to apply.` 라는 메시지를 출력한다.  
```python
INSTALLED_APPS = [
    "polls.apps.PollsConfig",
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
]
```
### 5.3. Migration 파일 생성
아래 명령어를 실행해 migration 파일을 생성한다.  
그럼 다음과같이 Model을 생성했다는 결과를 확인할 수 있고 polls/migrations에 migration 파일이 생성된다.  
여기까지 진행했을 때 아직 DB에는 반영된 상태가 아니다.  
이어서 진행할 migrate 명령을 실행해야 DB에 적용된다.
```sh
$ python manage.py makemigrations polls
Migrations for 'polls':
  polls/migrations/0001_initial.py
    - Create model Question
    - Create model Choice
```

### 5.4. Migrate 실행
Migrate를 실행하기 전에 실제로 어떤 SQL이 실행되는지가 궁금하면 `sqlmigrate` 명령어를 실행해 확인해볼 수 있다.  0001은 생성된 migration 파일의 번호다.
```sh
$ python manage.py sqlmigrate polls 0001
```

migration을 실행하기 전에 다른 문제가 없는지 확인하려면 `check` 명령어를 실행해서 확인할 수 있다.
```sh
$ python manage.py check
System check identified no issues (0 silenced).
```

`migrate` 명령을 실행하면 실제로 DB에 테이블이 생성된다.  
생성한 테이블 이름은 prefix로 app 이름이 붙어서 각각 polls_question, polls_choice 로 생성된다.
```sh
$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Applying polls.0001_initial... OK
```

## 6. Django admin 설정

### 6.1. 관리자 생성
```sh
$ python manage.py createsuperuser
```

## 7. Django test코드 실행
```sh
$ python manage.py test polls
Found 1 test(s).
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F
======================================================================
FAIL: test_was_published_recently_with_future_question (polls.tests.QuestionModelTests.test_was_published_recently_with_future_question)
was_published_recently() returns False for questions whose pub_date is in the future.
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/sisung/iksflow/django-playground/mysite/polls/tests.py", line 16, in test_was_published_recently_with_future_question
    self.assertIs(future_question.was_published_recently(), False)
AssertionError: True is not False

----------------------------------------------------------------------
Ran 1 test in 0.002s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

## 8. Django debug toolbar 설치
아래 페이지의 Installation을 참고해 설치.
https://django-debug-toolbar.readthedocs.io/en/latest/installation.html

### 8.1. 의존성 설치
```sh
$ python -m pip install django-debug-toolbar
```

### 8.2. start
```python
INSTALLED_APPS = [
    # ...
    "django.contrib.staticfiles",
    # ...
]

STATIC_URL = "static/"
```


