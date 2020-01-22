---
layout: manualpost
title: Django EZ Manual
author: 뷰와 템플릿, 정적 파일
icon: fa-lightbulb
icon-style: regular
layout: manualpost
---

<h2>4. 뷰와 템플릿</h2>

앱의 디렉토리 내의  
`views.py` 에서 메소드는 `urls.py` 에서 주는 인자값을 받을 수 있다.  

이게 무슨 말인지 예제를 보자.  

<hr/>

`[앱 이름]/views.py`

```python
def main(request, id):
    return HttpResponse("find %s." % id)
```

[앱 이름]/urls.py

```python
urlpatterns = [
    ...
    path('<int:id>/', views.main, name=main)
]
```

<hr/>

어떤 과정으로 전달이 되는 것인지 알아보자.  

`http://127.0.0.1:8000/[앱 이름]/5/` 로 접속하면  
`[프로젝트 이름]/settings.py` 에서 ROOT_URLCONF 설정에 의한  
`[프로젝트 이름]/urls.py` 모듈을 보게된다. urlpatterns 를 찾고  
`[앱 이름]/` 을 찾고 따라 들어가서 `[앱 이름]/urls.py` 에 있는 urlpatterns 에 따라  
남은 `5/` 를 처리한다. urlpatterns 에서 `<int:id>/` 와 일치함으로  
`views.py` 의 main 메소드에 인자 값을 넘겨준다.  

어떤 의미인지 아직도 이해를 못한다면 지금이라도 직접 따라해보자 글만 읽는 것보다 직접 해보는 것이  
이해하는데 큰 도움을 줄 수 있다.  

<hr/>

<h3> views.py 의 기능 </h3>

우리는 views 에서 데이터베이스를 사용할 수도 있고 Python Third Party 템플릿 시스템을 사용할 수도 있다.  
`views.py` 는 PDF 를 생성하거나 XML 을 출력하거나, 실시간으로 ZIP 파일을 만들 수도 있다.  
`views.py` 는 Python 의 어떠한 라이브러리도 사용할 수 있다.

<h4>하지만 여기서 중요한 점!</h4>

`views.py` 에 웹 코드와 디자인이 모두 하드코딩이 되어 있다면  
앱의 크기가 커질 수록 보기 매우 좋지 않고 업무를 분담하여 작업하기가 힘들 것이다...  
그래서 우리는 <strong>"분할"</strong> 을 해야한다.  
<strong>"분할"</strong> 을 위해서는 Django 의 <strong>템플릿</strong> 시스템을 이용해야 한다.

<h3> 템플릿을 만들자 </h3>

`[앱 이름]/templates` 라는 디렉토리르 만들자.  
그리고, `[앱 이름]/templates/[앱 이름]` 디렉토리를 하나 만들자.

그럼, "왜 templates 디렉토리안에 `[앱 이름]` 디렉토리를 왜 또 만드냐??" 라는 질문을 할 것 같은데.  
그에 대한 대답은 "구분하기 위해서" 이다.
자세한 설명은 다음과 같다.

```text
"Templates NameSpacing"

우리는 프로젝트를 진행할 때 하나 이상의 앱을 사용할 것이다. 
당연하게도 우리는 Django 내의 앱을 이용할 수도 다른 사람의 앱을 이용할 수도 있다.
그런데, 각각의 앱의 Templates 들을 구분하기 위해서는 그 Template 들이 
"누구"의 것인지 구분해야 할 필요가 있다.
그렇기 때문에 templates/[앱 이름] 디렉토리를 만드는 것이다.

그런데, 여기서 "templates 의 상위 디렉토리 이름을 보고 구분하면 되는것 아님?" 라고 궁금증이 생길 수도 있다.
하지만, 나는 Django 프레임워크를 만든 사람이 아니기 때문에 대답하지 않겠다.
나의 개인적인 생각이지만 아마 Templates 의 상위 디렉토리는 보지 않기 때문에 그런것 같다. (당연한 얘기인가?)
```

이제 우리는 Django 의 템플릿 시스템을 이용하기 위한 준비가 되었다.  
`[앱 이름]/templates/[앱 이름]` 디렉토리에 `main.html` 을 만들어보자 (물론 굳이 main.html 이 아니어도 좋다.)

우리는 템플릿에 웹 코드를 작성할 것임으로 `[앱 이름]/views.py` 를 수정하여야 한다.  

```python
from django.template import loader
from django.http import HttpResponse

def main(request):
    template = loader.get_template('[앱 이름]/main.html')
    return HttpResponse(template.render(request))
```
간단하지 않은가? 하지만 Django 에서는 더 간단하게 만들 수도 있다.  

Django 에서 지원하는 `render()` 함수를 사용하여 보자.  

```python
from django.shortcuts import render

def main(request):
    return render(request, '[앱 이름]/main.html')
```

더 간결해진 것을 확인할 수 있다.  

<h4>render() 에 대해서...</h4>

```text
render() 는 1 번 인수로 request 객체를 받고 2 번 인수로는 템플릿의 이름을 받는다. 
3 번 인수는 "옵션"인데 context 사전형 객체를 선택적으로 받는다.

render() 는 템플릿의 HttpResponse 객체를 반환해준다.
```

<hr/>

<h4>404 에러 발생하기</h4>

만약 데이터베이스에서 데이터를 불러오지 못할 때  
간단하게 404 에러를 발생하여 보자.  

Ex)  
만약 `models.py` 에서 설정한 Question 필드의 데이터를 불러오려 한다고 가정하자.  
당연하게도 `models.py` 의 Question 을 import를 해야한다.

```python
...
from .models import Question

def main(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, '[앱 이름]/main.html', {'question': question})
``` 

위 코드를 분석해 보면 `question_id` 값이 Question 필드 데이터베이스에 존재하지 않으면  
404 에러를 발생시키고, 존재하면 그 값을 context에 담아 `HttpResponse` 객체를 반환하는 것을 알 수 있다.  

<hr/>

<h2>5. 정적 파일</h2>

프로젝트를 진행하다 보면 정적 데이터를 사용해야 할 때가 온다.  
Django 에서는 그 정적 파일을 static 디렉토리에서 관리한다.  
static 에는 CSS 와 같은 stylesheet, 이미지 정보, JS 등을 넣을 수 있다.  
이로써 우리는 웹을 좀 더 풍부하게 만들 수 있다.  
다음과 같이 만들어보자.  

`[앱 이름]/static` 디렉토리를 만들고  
`[앱 이름]/static/[앱 이름]` 디렉토리를 만들자. (templates 와 구성이 비슷하다!)  


`[앱 이름]/static/[앱 이름]/style.css` 를 만들고 실제 적용을 해보자.  
우리는 `[앱 이름]/templates/[앱 이름]/main.html` 파일의 맨 윗 줄에 `{% load static %}` 한 줄만 추가하면  
static 폴더의 파일들을 모두 쓸 수 있다.  
다른 파일들은 "알아서" 응용하자.

<hr/>

다음 <strong> "6. 테스팅" </strong> 는 <strong>DJANGO-MANUAL4</strong> 에서 이어진다.