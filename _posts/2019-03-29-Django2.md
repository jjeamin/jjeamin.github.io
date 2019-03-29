---
layout: post
title:  "Django 배포하기"
summary: "Django를 배포해보자"
date:   2019-03-29 22:00 -0400
categories: web
---

# dependency
- github
- pythonanywhere

# Git
이전 까지 만들었던 페이지들을 깃허브에 원격 저장소와 연결 시켜주면 됩니다.

1. 깃허브 원격 저장소를 웹에서 생성하기
2. git clone
3. 내가 만든 사이트 폴더안에 파일들을 원격 저장소에 넣기
3. git commit
4. git pull


# pythonanywhere

- pythonanywhere consoles 들어가기[bash 선택]

- git clone 블로그 가져오기

```
git clone https://github.com/jjeamin/django.git
```

- 가상환경 생성하기

```
cd django

virtualenv --python=python3.6 myvenv

source myvenv/bin/activate

pip install django~=2.0
```

**조금 오래 걸립니다.**

- 데이터 베이스 생성

```
python manage.py migrate

python manage.py createsuperuser
```

# web app 으로 블로그 배포하기

- 뒤로 가서 web을 선택한다.

- manual config 선택

- python 3.6 선택

- virtualenv로 가서 가상환경 경로 적기

# wegi 파일 설정하기

- wsgi로 가서 코드를 다 지우고 아래 코드를 넣는다.

```python
import os
import sys

path = '/home/<your-PythonAnywhere-username>/my-first-blog'  # PythonAnywhere 계정으로 바꾸세요.
if path not in sys.path:
    sys.path.append(path)

os.environ['DJANGO_SETTINGS_MODULE'] = 'mysite.settings'

from django.core.wsgi import get_wsgi_application
from django.contrib.staticfiles.handlers import StaticFilesHandler
application = StaticFilesHandler(get_wsgi_application())
```


# SUCESS

`/admin/` 을 추가해 들어가면 사이트를 관리 할 수 있습니다.
