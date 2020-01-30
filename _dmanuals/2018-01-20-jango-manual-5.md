---
layout: post
title: 테스팅, 앱 패키징
icon: fa-lightbulb
icon-style: regular
order: 6
---

<h2>6. 테스팅</h2>

<h4>테스트를 만들어야 하는 이유</h4>

"아니 힘들게 왜 만들어야 하는거지?" 라고 생각할 수도 있고,  
"굳이 지금?" 이라고 생각할 수도 있다.  
자동화된 테스트를 만드는 것이 이 프로젝트와 앱을 작동하게 하는 것도 아니지만  
더 많은 것을 하기 위해서는 자동화된 테스트를 작성하는 것이 좋다.  
점점 더 정교한 어플리케이션을 만들어가면 수많은 복잡한 상호작용이 있을 것이다.  
수동적으로 일일이 다 테스트를 하는 것보다 몇초만에 테스트를 끝낼 수 있다면 귀한 시간을 아낄 것이다.  
또한, 테스트를 통해 예기치 않은 동작을 일으키는 코드를 식별하는데 큰 도움을 얻을 수 있다.  

테스트는 문제를 식별하는 것이 아닌 예방이라고 볼 수 있다.  
테스트가 없으면 어플리케이션의 목적 또는 의도된 동작이 불투명 할 수 있고,  
심지어 자신의 코드임에도 불구하고 정확히 무엇을 하고 있는지 알아내려고 노력을 할 수도 있다.  

테스트는 팀이 함께 일하는것에 큰 도움을 준다.  
테스트는 동료가 실수로 코드를 손상시키지 않는다는 것을 보증해 줄 수 있다.  

tests.py 에서 어떻게 테스트케이스를 짜야하는지에 대해서는 설명하지 않겠다.  
하지만 테스트케이스를 짰다면 다음의 명령어로 테스팅을 할 수 있다.  

`$ python manage.py test [앱 이름]`

<hr/>

<h2>7. 재사용 가능한 앱 패키징</h2>

<h4>앱 패키징 하기</h4>

1. 먼저 Django 프로젝트 디렉토리 외부에 앱을 담을 디렉토리를 만드는데 이때 이름은  
`"django-[앱 이름]"` 으로 설정하는 것을 권장한다.  

2. `django-[앱 이름]` 디렉토리로 앱을 옮긴다.  

3. `django-[앱 이름]` 에 `README.rst` 파일을 만든다.   
여기서 README.rst 파일은 앱의 "표지", 어떻게 사용하는지 알려주는 "목차" 역할을 한다.

4. `django-[앱 이름]` 에 `LICENSE` 파일을 만든다.  
대부분 Django 호환 앱은 BSD 라이센스로 배포된다.  

5. `django-[앱 이름]` 에 `setup.cfg` 와 `setup.py` 를 만든다.  

<hr/>

`setup.cfg` 예시

```buildoutcfg
[metadata]
name = django-[앱 이름]
version = 0.1
description = [설명]
long_description = file: README.rst
url = https://www.example.com/
author = jinho
author_email = example@gmail.com
license = BSD-3-Clause # Example license
classifiers =
    Environment :: Web Environment
    Framework :: Django
    Framework :: Django :: X.Y # Replace "X.Y" as appropriate
    Intended Audience :: Developers
    License :: OSI Approved :: BSD License
    Operating System :: OS Independent
    Programing Language :: Python
    Programing Language :: Python :: 3
    Programing Language :: Python :: 3 :: Only
    Programing Language :: Python :: 3.6
    Programing Language :: Python :: 3.7
    Programing Language :: Python :: 3.8
    Topic :: Internet :: WWW/HTTP
    Topic :: Internet :: WWW/HTTP :: Dynamic Content

[options]
include_package_data = true
packages = find:
```

<hr/>

`setup.py` 예시 (간단하다.)

```python
from setuptools import setup

setup()
```

<hr/>

`django-[앱 이름]` 에 `MANIFEST.in` 파일을 만든다.  
지금까지 우리는 파이썬 모듈과 패키지만 포함했는데 추가 파일을 포함시키기 위해서는  
`MANIFEST.in` 파일에 작성하여 포함시켜야 한다.  

`MANIFEST.in` 예시

```text
include LICENSE
include README.rst
recursive-include polls/static *
recursive-include polls/templates *
recursive-include docs *
```

<hr/>

선택 사항이지만 자세한 설명을 하는 문서를 추가 시키는 것을 추천한다.  
`django-[앱 이름]` 에 docs 라는 빈 디렉토리를 생성하고 `MANIFEST.in` 에 다음 한 줄을 추가한다.  
`recursive-include docs *`

<hr/>

`$ python setup.py sdist` 를 입력하여 패키지를 빌드한다.  

`"django-[앱 이름]-0.1.tar.gz"` 라는 파일이 생성되면  
패키지 만들기에 성공한 것이다.  

우리가 만든 패키지를 다시 설치해보자 프로젝트 파일에서 다음의 명령어를 입력한다.  
`$ python -m pip install --user django-[앱 이름]/dist/django-[앱 이름]-0.1.tar.gz`

프로젝트를 다시 실행시켜보면 정상적으로 작동한다.  

패키지 삭제는 다음의 명령어를 입력한다.  
`$ python -m pip uninstall django-[앱 이름]`

<hr/>

<h3>여기까지...</h3>

지금까지 우리는 어플리케이션을 어떻게 구조적으로 만드는지부터 패키지를 제작하는 단계까지 알아보았다.  
아직, Django 내부 템플릿이나 내부 기능들에 대해 설명을 하지 않았지만 그래도 Django 를 어떻게 써야 하는지  
감을 잡는데 도움되었으면 한다.  
부족한 내용이 있다면 수정도 하고 추가적인 내용도 덧붙일 계획이 있으니  
개선할 내용 및 알고싶은 내용이 있다면 `max000807@gmail.com` 으로 메일을 보내도 좋다.