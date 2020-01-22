---
layout: manualpost
title: Django EZ Manual
author: URL 요청과 응답
icon: fa-lightbulb
icon-style: regular
layout: manualpost
---

<h2>2. URL 요청과 응답</h2>

<h3>첫 번째 뷰 제작</h3>

`[앱 이름]/views.py` 에 들어가 다음의 코드를 입력해 보자

```python
from django.http import HttpResponse

def main(request):
    return HttpResponse("Hello")
```

앱을 처음 제작하면 `[앱 이름]` 디렉토리 안에 `urls.py` 가 없다.
만들도록 하자.

이 `[앱 이름]/urls.py` 를 이용해 `views.py` 에 있는 함수(method)를 호출한다.
그럼 이제 최상위 `[프로젝트 이름]/urls.py` 에서 `[앱 이름]/urls.py` 를 보게 설정해야 한다.
`[프로젝트 이름]/urls.py` 에 들어가서

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('[앱 이름]/', include('[앱 이름].urls')),
    path('admin/', admin.site.urls),
]
```

그럼 이제 `[앱 이름]/urls.py` 가 `views.py` 를 보게 하자.
`[앱 이름]/urls.py` 에 들어가서

```python
from django.urls import path
from . import views

app_name = '[앱 이름]'
urlpatterns = [
    # ex: /polls/
    path('', views.main, name='main'),
]
```

<strong> 파이썬을 잘 안다면 어떤 것의 이름은 자기가 알아서 설정해도 된다는 것을 알 것이다. </strong>

<h4>path() 함수에 대한 설명</h4>

```text
path() : 2개의 필수 인수 route, view // 2개의 선택 인수 kwargs, name 총 4개의 인수가 있다.
path() : route
        일치하는 패턴을 받는다. ex) 값이 'main' 이라 하면  ==> http://127.0.0.1/main/ 에서 "/main/" 과 매치됨
path() : view
        일치하는 패턴을 찾으면 HttpRequest 객체를 첫번째 인수로 하고 경로로부터 '캡처된' 값을 키워드로 함수 호출
        ex) views.main ==> views.py 의 main 함수 호출
path() : kwargs
        임의의 키워드 인수를 view에 사전형으로 전달한다.
path() : name
        URL 에 이름을 짓는다. 이를 이용하여 템플릿을 포함한 Django 어디에나 명확하게 참조가 가능하다.
        단 하나의 파일만 수정해도 프로젝트 내의 모든 URL 패턴을 바꿀 수 있도록 도와준다. (URL Reversing을 위해)
```

<h3>요청과 응답의 과정</h3>

```text
"클라이언트" ==127.0.0.1:8000/[앱 이름]/==> "서버"
                        전달
"서버":
1. [프로젝트 이름]/urls.py
2. [앱 이름]/urls.py
3. [앱 이름]/views.py

1. --> 2. --> 3.

"서버" : 1. [프로젝트 이름]/urls.py 에서 '/[앱 이름]/' 확인 후 [앱 이름]/urls.py 호출
         2. [앱 이름]/urls.py 에서 /'' 확인 views.main 함수 호출
         3. views.main 함수 리턴 값 반환
```

다음 <strong> "3. 모델과 관리자페이지" </strong> 는 <strong>DJANGO-MANUAL2</strong> 에서 이어진다.
