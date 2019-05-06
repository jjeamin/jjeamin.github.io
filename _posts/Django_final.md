---
layout: post
title:  "Django 분석하기"
summary: "Django 분석하기"
date:   2019-05-16 12:00 -0400
categories: Django
---

# Django를 제대로 알아보자

MVC 패턴이다.

1. model : models.py를 정의해서 db 구조를 만든다.
2. view : templetes 로 어떤 화면을 보여줄지 결정한다.
3. controller - views.py 에서 데이터를 읽어, index.html에 전달


## Table
- models.py
- admin.py

## 어플리케이션 Logic
- views.py

## 화면 UI
- templates/app/<name>.html

---

## settings.py
- 프로젝트 설정 파일

1. db 설정(mysql 기준)

```
DATABASES = {
    'default': {
        'ENGINE' : 'django.db.backends.mysql',
        'NAME' : 'DB0227',
        'USER' : '<db_user>',
        'PASSWORD' : '<db_passwd>',
        'HOST' : 'localhost',
        'PORT' : ''
    }
}
```

2. templates 설정

```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': ['',],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

- 경로를 잘잡아줘야함

3. static 설정

```
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

4. APP 등록

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    <추가하기>,
]
```

- 개발하는 모든 앱은 여기에 등록해야한다.

5. 시간 설정

```
LANGUAGE_CODE = 'ko-kr'

TIME_ZONE = 'Asia/Seoul'
```

---

## models.py
- ORM(Object Relation Mapping) 기법을 사용한다. 즉, 클래스 객체에서 CRUD(Create,Read,Update,Delete)를 수행하면 자동으로 DB에 반영시킨다.

- 마이그레이션 : DB변경사항을 알려주는 정보

---

## urls.py
- app url
- project url

---

## views.py
- Logic을 코딩하는 가장 중요한 파일이다.

---

## templates

- 웹 화면 별로 템플릿 파일이 하나씩 필요하며 웹 개발시 여러개의 html 파일을 모아두는 곳

 ---

## Bookmark APP 구조 만들기

```
django-admin.py startproject website
cd website
vi settings.py
```

- template

```
TEMPLATES = [
  'DIRS' : [os.path.join(BASE_DIR,'templates'),]
]
```

- static

```
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

- time

```
TIME_ZONE = 'Asia/Seoul'
```

- media : 업로드 기능

```
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

- migrate

```
python manage.py migrate
```

- superuser

```
python manage.py createsuperuser
```

- app 생성

```
python manage.py startapp bookmark
```

- app 등록

```
vi settings.py

INSTALLED_APPS = [
  'bookmark',
]
```

- models.py 정의하기

models.py와 admin.py는 세트다

- admin.py에 등록하기

```
from django.contrib import admin
from bookmark.models import Bookmark

class BookmarkAdmin(admin,ModelAdmin):
  list_display = ('title','url')

admin.site.register(Bookmark,BookmarkAdmin)
```

BookmarkAdmin은 Bookmark 클래스가 admin 사이트에서 어떤 모습으로 보여줄지 정의하는 클래스다. title과 url만 보일 것이다.

- migrate

```
python manage.py makemigrations
python manage.py migrate
```

테이블의 새로운 변경사항을 반영하는 작업

## Admin에 추가완료

- urls.py 설정

```
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('bookmark/',include('bookmark.urls'))
    path('admin/', admin.site.urls),
]
```

- views.py 설정 : Logic

- template 설정

```
mkdir templates
cd templates
mkdir bookmark
cd bookmark
vi bookmark_list.html
```

- html 작성
