---
layout: post
title: Django EZ Manual
author: 모델과 관리자페이지
icon: fa-lightbulb
icon-style: regular
order: 4
---

<h2>3. 모델과 관리자페이지</h2>

여기서는 데이터베이스를 설치하고 첫 모델을 생성한 후,
Django 에서 자동 생성되는 관리자 사이트에 대해 설명한다.
자 어서 알아보도록 하자.

<h4>하지만 그 전에!!</h4>

<strong>
Django 에서는  mysql, postgresql, sqlite3, oracle 등 다양한 DB 를 사용할 수 있다.
이 설명에는 MySQL을 사용하여 설명할 것이다.
지금부터 설명은, MySQL 이 설치되어 있다는 가정하에 시작한다.
</strong>

<br>
<br>

Django 에서 `models.py` 의 역할은 데이터베이스 설정 및 관리의 역할을 한다.
Django 와 MySQL 연동을 위해 다음의 명령어를 입력하자.
`$ pip install mysqlclient`

Django 프로젝트에 사용할 DATABASE를 미리 만들어야 한다.
DATABASE를 만들도록 하자.

이제 우리는 `[프로젝트 이름]/settings.py` 에 들어가 DATABASE = {...} 부분을 수정해야 한다.

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': '[DATABASE 이름]',
        'USER': '[DB 권한 USER 이름]',
        'PASSWORD': '[DB 권한 USER PASSWORD]',
        'HOST': '127.0.0.1',
        'POST': '3306',
        'OPTIONS': {
            'init_command':'SET sql_mode="STRICT_TRANS_TABLES"'
        }
    }
}
```

끝났다면 TIME_ZONE 을 설정하자.
한국시 설정:

```python
TIME_ZONE='Asia/Seoul'
USE_TZ=False
```

나중에, 클라이언트에 표시되는 시간은 한국 시간이지만 DB 에 저장되는 시간이 다르다면 자기가 스스로 왜 그런지 알아보자.
<h6>사실 from django.utils import timezone 을 하면 된다. 이건 비밀이다.</h6>

INSTALLED_APPS 의 의미는 프로젝트내에서 사용할 앱 들을 말한다.
우리가 만든 앱을 사용하고 싶다면 INSTALLED_APPS 에 추가하면 된다. (하지만, 그 전에 데이터베이스 설정을 먼저 끝내자)

가끔씩, 우리는 DB 연결은 미리 하고 싶지만 TABLE 설정은 바로 안하고 싶어할 수도 있다.

<strong>그렇다, 안하면 된다.</strong>

TABLE 설정을 하지 말고 다음의 명령어를 입력하여 보자.
<h6>믿어도 된다.</h6>

`$python manage.py migrate`

그렇다면, 자신이 설정한 DATABASE 에는 다음과 같은 10 개의 TABLE 들이 생겼을 것이다.

```text
auth_group                      django_admin_log
auth_group_permissions          django_content_type
auth_permission                 django_migrations
auth_user                       django_session
auth_user_groups
auth_user_user_permissions
```

위의 TABLE 들의 의미는 뭘까?
그렇다, INSTALLED_APPS 에 있는 기능들이 사용할 TABLE 들이 되시겠다.

이제 어느덧 우리가 만든 앱에서 사용할 TABLE 이 필요한 순간이 왔다.
어떻게 우리만의 TABLE 들을 Django 내에서 만들 수 있을까?

그렇다, `[앱 이름]/models.py` 의 필드 영역을 이용하면 된다.

지금부터는 예시와 함꼐 설명을 하겠다.

```python
from django.db import models

# Create your models here.
class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

한번 이해하면 어느 순간 장고로 DB 를 설계하는 자신을 보게 될 것이다. (아마도..?)
데이터베이스의 각 필드는 Field 클래스의 인스턴스로서 표현된다. (이게 이해하기 어렵나?)
<strong>아주 쉽게</strong> 설명을 해보자면 현재 2 개의 클래스가 있다.
바로 Question 과 Choice 이 클래스들의 이름은 TABLE 들의 이름이 된다.

Question 은 `"[앱 이름]_question"`
Choice 는 `"[앱 이름]_choice"` 로 저장된다.

TABLE 의 이름을 설정하는 법 참 쉽다.

클래스내에는 인스턴스가 있다.
Question 클래스를 보면 `question_text`, `pub_date` 라는 인스턴스가 있다.
이 둘의 인스턴스 이름은 TABLE 의 Cloumn 명으로 된다.

column 명 설정하기 참 쉽다.

아니 그럼 인스턴스의 속성은 무엇인가..?
`question_text` 를 보면 `models.CharField(max_length=200)` 라고 되어있는데 이게 도대체 뭘까?
그렇다, Column Type 이 되겠다.

`models.CharField(max_length=200)` 은 `varchar(200)`을 의미하고,
`models.DateTimeField('date published')` 는 `datetime` 을 나타낸다.
당연하게도 각각의 인수값을 이용하여 Column 의 기본 값 설정 및 제한 또한 설정 가능하다.

Column Type 설정하기 참 쉽다.

여기서 의문, 그럼 Identity 설정 즉, Primary Key 는 왜 설정 안하는가?
그렇다, Django 에서는 자동으로 PK 를 만들어준다. (Column 명은 id 로 되어있다.)

참 편하다.

Choice 클래스를 보면 `ForeignKey` 를 사용한 인스턴스가 있는데 이것은 무엇일까?
바로, DB간 다-대-일(many-to-one), 다-대-다(many-to-many), 일-대-일(one-to-one) 과 같은 DB 관계설정을 할 수 있다.


DB 간 관계를 설정하는 것은 중요하니 ForeignKey 메소드에 대한 설명을 하겠다.

```text
ForeignKey([클래스 이름], [on_delete 필드])
1. CASCADE : ForeignKey 가 바라보는 값이 삭제될 때
             ForeignKey 를 포함하는 Row 도 삭제.
2. PROTECT : ForeignKey 가 바라보는 값이 삭제될 때
             ForeignKey 를 포함하는 Row 는 삭제되지 않도록 Protected Error 발생.
3. SET_NULL(null=True) : ForeignKey 가 바라보는 값이 삭제될 때
                ForeignKey 를 포함하는 Row 값을 Null 로 바꿈, (null=True 일 때).
4. SET_DEFAULT(default=[값]) : ForeignKey 가 바라보는 값이 삭제될 때
                   ForeignKey 를 포함하는 Row 값을 default 값으로 바꿈.
5. SET() : ForeignKey 가 바라보는 값이 삭제될 때
           ForeignKey SET 에 설정된 함수에 의해 Row 값 설정.
6. DO_NOTHING : ForeignKey 가 바라보는 값이 삭제될 때
                아무 일도 일어나지 않음 (참조무결성을 해칠 위험이 있다.)
```

models.py 는 데이터베이스 설정 뿐만 아니라 데이터베이스를 관리를 할 수 있는 파일이므로
좀 더 많은 응용을 할 수 있지만 이 문서의 목적은 쉽고 빠르게 Django 에 대해 이해하는 문서이므로
말하지 않겠다.

우리는 각각의 입맛에 맞게 데이터베이스를 설정하였음으로 이제
`[프로젝트 이름]/settings.py` 의 INSTALLED_APPS 에 다음과 같이
우리가 생성한 앱도 사용하겠다고 추가하면 된다.

```python
INSTALLED_APPS = [
    '[앱 이름].apps.[apps.py 내의 클래스 이름]'
    ...
]
```

우리가 만든 앤도 사용하겠다고 추가했으니 다음의 명령을 입력하자.
`$ python manage.py makemigrations [앱 이름]`

그러면 `[앱 이름]/migrations/0001_initial.py` 가 추가되었을 것이다.
그리고 자신의 DATABASE 를 확인하여 보자.

잘 되었는가??

다음 <strong> "4. 뷰와 템플릿" </strong> 은 <strong>DJANGO-MANUAL3</strong> 에서 이어진다.