# Form

## Django form Class

- 유효성 검사 도구 중 하나
- Django의 경우 유효성 검사를 단순화 하고 자동화 할 수 있는 기능 제공
  - 안전하고 빠르게 수행 하는 코드 작성
- form이 수행하는 작업
  - 렌더링을 위한 데이터 준비 및 재구성
  - 데이터에 대한 HTML forms 생성
  - 클라이언트로부터 받은 데이터 수신 및 처리



### Form class

- Django Form 관리 시스템의 핵심
- Form내 field, 배치, 디스플레이 wiget, label, 초기값, 에러메세지 결정



### 사용법

#### Form 사용

1. **Form 선언**
   1. 만든 app에서 forms.py 라는 파일 생성
   2. django 에서 forms import
   3. 클래스 선언

```python
# articles/forms.py					# articles = 앱 이름
from django import forms

class ArticleForm(forms.Form):		# forms 라이버러리에서 파생된 Form 클래스 상속받음
    title = forms.CharField(max_length = 20)
    content = forms.CharField(widget=forms.Textarea)
```

- Model 선언과 유사, 같은 필드타입 사용(일부 매개변수가 다름)



2.  **views.py & html 파일 변경**

```python
# articles/views.py
from .forms import ArticleForm		# .forms 에서 AricleForm class 가져옴

def new(request):				
    form = ArticleForm()
    context = {
        'form':form,
    }
    return render(request, 'articles/new.html' , context)
```

```django
articles/templates/articles/new.html
{% extends 'base.html' %}

{% block content %}
  <h1>NEW</h1>
  <hr>
  <form action="{% url 'articles:create' %}" method="POST">
    {% csrf_token %}
    
    {% comment %} 변경 전 코드 {% endcomment %}  
    <label for="title">Title: </label>
    <input type="text" id="title" name="title"><br>
    <label for="content">Content: </label>
    <textarea name="content" id="content" cols="30" rows="10"></textarea>
    
    {% comment %} 변경 후 코드 {% endcomment %}
    {{ form.as_p}}
      
      <input type="submit">
  </form>
  <a href="{% url 'articles:index' %}">back</a>
{% endblock content %}

```

- .as_p 는 form의 필드를 `<p>`태그로 하나씩 처리하여 랜더링

  - as_ul() : 각 필드가 `<li>`태그로 감싸져서 랜더링
    - `<ul>` 태그는 직접 작성해야 함

  - as_table()
    - 각 필드가 `<tr>`태그 행으로 감싸져서 랜더링
    - `<table>`태그는 직접 작성해야 함

#### HTML input 요소 표현 방법

1. Form fields
   - input에 대한 유효성 검사 로직을 처리하며 템플릿에서 직접 사용 됨
2. Widgets
   - 웹 페이지의 HTML input 요소 렌더링
   - GET/POST 딕셔너리에서 데이터 추출
   - widgets은 Form fields 에 할당

#### Widgets

- HTML 렌더링 처리
- Form Fields와는 다름
  - Form Fields는 input 유효성 검사 처리
  - Widgets은 웹 페이지에서 단순 렌더링 처리

```python
from django import forms

class UseOS(forms.Form):
    OS1 = 'windows'
    OS2 = 'mac'
    OS3 = 'linux'
    # 웹에선 윈도우,맥,리눅스로 표기됨
    OS_Choices = [
        (OS1,'윈도우'),
        (OS2,'맥'),
        (OS3,'리눅스'),        
    ]
    title = forms.CharField(max_length = 20)
    content = forms.CharField(widget=forms.Textarea)    
    OS = forms.ChoiceField(choices=OS_Choices, widget = forms.Select())
```

<hr>

## ModelForm

- 위의 방식을 사용시 Model에 정의한 필드를 Form에서 재정의 해야 함
- 이를 간단히 해결하기 위해 Model을 통해 Form Class를 만들 수 있는 ModelForm이라는 도구 제공
- 회원 가입과 같은 전체 데이터 입력 필요시 주로 사용
- forms.py 는 어느 위치에 있어도(model 내부에 있어도) 상관 없으나, 수정을 위해 app폴더/forms.py에 작성

```python
# models.py
from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=10)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.title
```

```python
# articles/forms.py 기존 방법					
from django import forms

class ArticleForm(forms.Form):		
    title = forms.CharField(max_length = 20)
    content = forms.CharField(widget=forms.Textarea)
```

```python
# articles/forms.py ModelForm 사용
from django import forms
from .models import Article

class ArticleForm(forms.ModelForm):		# ModelForm 상속
    class Meta:							# Model 정보 작성하는 곳
        model = Article					# models.py의 Article model
        fields = '__all__'				# 입력 받아야 하는 form 표기(created_at 등 제외) 
        # exclude = ('title')			# title 제외하고 표기, fields와 함께 사용 불가
```

#### Meta class

- Model의 정보를 작성하는 곳
- ModelForm 사용시 사용할 모델을 적용하기 위해 사용



### ModelForm을 사용하여 view 수정

```python
def create(request):
    if request.method == 'POST':
        form = ArticleForm(request.POST)
        if form.is_valid():
            article = form.save()
            return redirect('articles:detail', article.pk)
    else:
        form = ArticleForm()
    context= {
        'form':form,
    }
    return render(request,'articles/create.html', context)
```

#### is_valid() method

- Django에서 제공하는 데이터 유효성 검사 테스트
- 유효성 검사를 실행하여 T/F의 boolean으로 반환

#### save() method

- 받은 데이터로 DB 객체를 만들고 저장
- ModelForm의 class는 기존 모델의 instance를 받을 수 있음
  - 이를 이용하여 조건문을 통해 기존의 new와 create 통합가능
  - edit과 update 통합 가능
  
- 유효성이 확인되지 않은 경우 save() 호출시 form.errors를 통해 에러 확인 가능

```django
{% block content %}
  <h1>CREATE</h1>
  <form action="{% url 'articles:create' %}" method="POST">
```

- form 태그의 action 값이 없어도 동작 가능은 하지만, AS를 위해 넣어주자
  - action 값이 없을 경우 현재 url을 다시 불러옴
  - method가 POST방식으로 보내짐 => views if문에서 달라짐 




### Widgets 활용

```python
class ArticleForm(forms.ModelForm):
    title = forms.CharField(
        label='제목',
        widget=forms.TextInput(
            attrs={
                'calss' : 'classname'
                'placeholder' : 'Enter the title',
                'maxlength' : 10,
            }
        )
        )
    content = forms.CharField(widget=forms.Textarea)  
    class Meta:
        model = Article
        fields = '__all__'
```

- class Meta내에 widgets을 사용 할 수도 있지만, 위와 같은 방법을 권장

  

### Form & ModelForm 비교

#### Form

- 어떤 Model에 저장해야 하는지 알 수 없으므로 검사 이후 cleaned_data 딕셔너리 생성
- cleaned_data 딕셔너리에서 데이터를 가져온 후 .save()호출

- Model에 연관되지 않은 데이터를 받을 때 사용

#### ModelForm

- Django가 해당 model에서 양식에 필요한 대부분의 정보를 가진것을 이용
- 바로 .save() 호출 가능



##### cleaned_data

```python
def create(request):
  	if request.metho =='POST':  
    	form = ArticleForm(request.POST)
         if form.is_valid():
            #이부분부터 cleaned_data
            title = form.cleaned_data.get('title')
            content = form.cleaned_data.get('content')
            article = Article.objects.create(title=title, content=content)
            return redirect(article)
```



<hr>

## Rendering fields manually

### Rendering fields manually

```django
<form action="" method="POST">
    {% csrf_token %}
    <div>
        {{ form.title.errors }}
        {{ form.title.label_tag}}
        {{ form.title }}
    </div>
    <div>
        {{ form.content.errors }}
        {{ form.content.label_tag }}
        {{ form.content }}
    </div>
    <button class="btn">작성</button>
</form>
```

- {{ form }}을 위와 같이 분리 가능

### Looping over the form's fields

```django
<form action="" method="POST">
    {% csrf_token %}
    {% for field in form %}
    	{{ field.errors }}
    	{{ field.label_tag }}
    	{{ field }}
    {% endfor %}
    <button class="btn">작성</button>
</form>
```



### Bootstrap Form 적용

#### TYPE1

- https://getbootstrap.com/docs/5.1/forms/overview/ 참조
- widget에 **'class' : 'form-control' **작성

```django
<form action="" method="POST">
    {% csrf_token %}
    {% for field in form %}
    	{% if field.errors %}	
    		{% for error in field.errors %}
    			<div class = "alert alert-warning" role="alert">{{ error|escape }}</div>
        	{% endfor %}
  		{% endif %}
    	<div class="form-group">
            {{ field.label_tag }}
    		{{ field }}
    	</div>
    {% endfor %}
    <button class="btn">작성</button>
</form>
```

#### TYPE2

```
$ pip install django-bootstrap-v5
```

- setting.py - INSTALLED_APPS에 'bootstrap5' 추가

- base.html에 적용
  - 최상단 {% load bootstrap5 %}
  - 기존 bootstrap css주소에 {% bootstrap_css %}
  - 하단 js 주소에 {% bootstrap_javascript %}

- 각각의 html에 적용
  - base.html 확장 후 {% load bootstrap5 %}
  - bootstrap 적용할 곳에 {% bootstrap_form ~~~ %}형태로 작성

```django
{% extends 'base.html' %}
{% load bootstrap5 %}
{% block content %}
	<form action="" method="POST">
        {% csrf_token %}
        {% bootstrap_form form layout='horizontal'%}
        {% buttons submit=" Submit" reset="Cancel"%}{% endbuttons %}
	</form>
~~~
```

