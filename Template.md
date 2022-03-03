# Django Template

- 코드 작성 순서
  - urls
  - views
  - templates



## Django Template Language(DTL)

- 조건, 반복, 변수, 필터 등의 기능 제공

### Variable

```django
{{ 변수이름 }}
```

- render()를 통해 view.py에서 받은 변수를 template에서 사용하는 방법
- 변수 이름이 '_' 로 시작 불가
- 변수에 접근하기 위해 점탐색 문법 사용
  - choice.choice_text 형식

- render() 세번째 인자를 딕셔너리 형태{'key' : value}로 받음



### Filters

```django
{{ 변수이름|필터 }}
{{ name|lower }}
```

- 사용 가능한 Filter는 문서에서 찾아보자



### Tags

```django
{% tag %}
{% if %}{% endif %} 
# {% if %} {% endif %}사이를 반복
# {% if %} 는 시작태그, {% endif %}는 종료 태그
```

- 출력 텍스트를 만들때 사용
- 반복, 논리를 수행 가능

- {{ forloop.counter }}
  - 반복문 실행시 반복된 숫자만큼(1,2,3,4,...)가 출력
  - forloop.counter0
    - 0,1,2,3...
  - forlloop.recounter
    - 끝지점을 기준으로 현재가 몇번인지

### Comments

```django
{# 이것은 한 줄 주석입니다 #}
```

```django
{% comment %}
	이것은
	여러 줄
	주석입니다
{% endcomment %}
```



## 상속

- 코드의 재사용을 줄이기 위해 만들어짐

- 부모 템플릿에는 자식 템플릿의 공간을 만들어 둬야함

  ```django
  {% block content %} {% endblock %}
  ```

- 자식 템플릿은 부모 템플릿에서 확장됨을 표기

  ```django
  {% extends 'parents.html' %}
  ```

  - 템플릿 최상단에 작성 해야 함

- 보통은 외부 경로에 부모 템플릿을 작성
  - 작성 이후 settings.py 에서 TEMPLATES의 항목 DIRS에 상대 주소 기입(현 폴더위치 = BASE_DIR)
    - [BASE_DIR / 'parenttemplate',], 같은 형식



### {% include ' ' %}

- 템플릿을 불러와서 현재 템플릿에 포함 시키는 방법

- 다른 템플릿에서 코드 작성 이후 넣고 싶은 공간에 {% include '템플릿이름' %}을 통해 포함시킴

```django
<body>
  {% include '_nav.html' %}
</body>
```

