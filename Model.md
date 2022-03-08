# Model

## 목차

- Model
- ORM
- Migrations
- Database Api
- CRUD
- Admin Site



## Model

- **데이터를 구조화하고 조작 하기 위한 도구**
- 단일한 데이터에 대한 정보를 가짐
  - 사용자가 저장하는 데이터들의 필수적인 필드,동작 포함
    - 10자 이내 제한 같은 동작
- 저장된 데이터베이스의 구조(레이아웃)
- Django는 model을 통해 데이터에 접속하고 관리
- 일반적으로 각각의 model은 하나의 데이터베이스 테이블에 매핑 됨



### Database

- DB
  - 체계화된 데이터의 모임
- Query (쿼리)
  - SQL = Structured Query Language
  - 데이터 조회 명령어
  - 조건에 맞는 데이터를 추출, 조작



#### Database 기본 구조

- 스키마(Schema)
  - 데이터베이스에서 자료의 구조,표현방법,관계 등을 정의(structure)
  - age - int, name - text ....
- 테이블
  - 열(column), 행(recorde)의 모델을 사용해 조직된 데이터 집합
  - Excel 형식

- PK(Primary Key)
  - 기본키
  - 각 레코드의 고유값



## ORM

- Object-Relational-Mapping
- 객체 지향 프로그래밍 언어(Python...)를 사용하여 호환 되지 않는 유형의(SQL,DB) 시스템 간에(DJango-SQL) 데이터를 변환 하는 기술
- Django는 내장 ORM 사용

- 웹 개발 속도를 높이기 위해 사용

#### 장,단점

##### 장점

- SQL을 잘 몰라도 DB 조작 가능
- 객체 지향적 접근으로 인한 높은 생산성

##### 단점

- ORM만으로 완전한 서비스를 구현하기 어려움



## models.py

```python
from django.db import models

# appname/models.py
class Article(models.Model):
    title = models.CharField(max_length=101)
    content = models.TextField()
```

- django.models.Model 의 서브 클래스 형식으로 표현



### Model Field

- **CharField(max_length=None, options)**
  - 길이 제한이 있는 문자열
  - max_length는 필수

- **TextField(options)**
  - 글자 수 제한이 없는 문자열 데이터 저장

- **DateTime**
  - datetime.date
  - 날자 입력 기능(달력)

- **DateTimeField**
  - auto_now : 최종 수정 일자
    - models.DateTimeField(auto_now=True)
  - auto_now_add:최초 생성 일자
    - models.DateTimeField(auto_now_add=True)

- **integerField**
  - python에서 정수 데이터를 저장하는 필드

## Migrations

- Django가 model에 생긴 변화를 반영하는 방법

- 3단계
  - models.py
  - python manage.py makemigrations
  - python manage.py migrate

### commands

- **makemigrations**

  - model 변경에 따라 새로운 migration 만들때 사용

    ```
    python manage.py makemigrations
    ```

- **migrate**

  - migration을 DB에 반영하기 위해 사용

  - 실제 db에 반영하는 과정

  - 모델에서의 변경 사항들과 DB의 스키마가 동기화

    ```
    python manage.py migrate
    ```

    

- sqlmigrate

  - migration에 대한 SQL 구문을 보기 위해 사용

  - 어떻게 동작할지 확인 가능

    ```
    python manage.py sqlmigrate appname 0001
    ```

    

- showmigrations

  - 프로젝트 전체의 상태 확인

  - migration 파일이 migrate 됬는지 확인 가능

    ```
    python manage.py showmigrations
    ```

- vscode 에서 SQLite 확장 프로그렘 설치시 확인이 편하다



### DateField's options

- **auto_now_add**
  - 최초 생성 일자
  - ORM이 최초 insert시에만 현재 날자와 시간으로 갱신

- **auto_now**
  - 최종 수정 일자
  - ORM이 save  할 때마다 현재 날자와 시간으로 갱신



## Database API

- DB를 조작하기 위한 도구
- Model 생성시 Django가 자동으로 database-abstract API를 만듬

- Class Name. Manager. QuerySet API 형식의 구문
  - Article.objects.all()



### DB API

- DB를 조작하기 위한 도구

- Model 생성시 Django에서 database-abstract API를 자동으로 생성

- ```
  ClassName.Manager.QuerySet API 구조로 입력
  # Article.objects.all()
  ```

  

#### Manager

- Django 모델에 DB query 작업이 제공되는 인터페이스
- 기본적으로 Django model class에 objects라는 Manager 추가됨

#### QuerySet

- DB에서 전달받은 객체 목록
- DB로부터 조회,필터,정렬 등 수행 가능



### Django shell

- 일반 Python shell을 통해서는 장고 프로젝트 환경에 접근 힘듬

  ```
  $ python manage.py shell
  # 인식 불가 추가 경로 장성 필요
  from articles.mdels import Article 입력시 작동
  # shell 사용시마다 입력해야함
  ```

  

- 장고 프로젝트 설정이 load된 python shell을 활용

  ```
  $ pip install ipython		#컬러등의 ... ui 변경으로 편하게 쓰기 위해
  $ pip install django-extensions
  #settings.py에 가서 INSTALLED_APPS에 추가 해야함('django_extensions')
  
  $ python manage.py shell_plus
  ```

  

## CRUD

- Create(생성), Read(읽기), Update(갱신), Delete(삭제)



### Create

- Type1

```
# shell 활성화
>>> article = Article() #article이 Ariticle() class의 instance 선언
>>> article.title = 'type1' #인스턴스 변수에 값 할당
>>> article.content = 'create1'
>>> article.save()
# 확인
>>> article
>>> Article.objects.all()
>>> article.title
...
```

- Type2

```
>>> article = Article(title='type2', content='create2')
>>> article.save()
```

- Type3

```
>>> Article.objects.create(title='type3', content='create3')
```

#### Create method

- save()
  - 객체를 DB에 저장
  - save()전에는 객체의 ID값을 알 수 없음



### Read

- QuerySet API method를 사용해 조회, 두가지 분류
  - return new querysets
  - do not return querysets

#### all()

- 현재 QuerySet의 복사본을 반환

  ```
  >>> Article.objects.all()
  ```

#### get()

- 주어진 lookup 매개변수와 일치하는 객체를 반환

- 없으면 DoesNotExist 예외 발생, 둘 이상이면 MultipleObjectsReturned 예외 발생

- pk와 같은 고유성을 보장하는 조회에서 사용해야 함

  ```
  >>> article = Article.objects.get(pk=100)
  # 아직 pk 100이 없어서 DoesNotExist 발생
  >>> Article.object.get(content='create1')
  # content=create1이 두개 이상 있을경우 에러가 날 수 있으므로 주의
  ```

#### filter()

- 주어진 lookup 매개변수와 일치하는 객체를 포함하는 새로운 Queryset 반환

  ```
  >>> Article.objects.filter(content='create2')
  #<QuerySet [<Article:type2>]>
  ```

#### Update

- article instance 객체의 변수의 값을 변경 후 저장

  ```
  # 선택
  >>> article = Article.objects.get(pk=1)
  >>> article.title
  'type1'
  #변경, 저장
  >>> article.title = 'type1_update'
  >>> article.save()
  #변경 확인
  >>> article.title
  'type1_update'
  ```

#### Delete

- delete()

  - QuerySet의 모든 행에 대해 SQL 삭제 수행, 삭제된 객체수, 유형당 삭제수 딕셔너리 반환

  ```
  >>> article.delete()
  ```

  

#### Field lookups

- 조회 시 특정 검색 조건을 지정

- QuerySet 메서드 filter(), exclude(), get()에 대한 키워드 인수로 지정됨

  ```
  #pk값이 2 초과(gt) 반환
  >>> Article.objects.filter(pk__gt=2)
  #특정 단어가 포함시 반환
  >>> Article.objects.filter(content__contains='create')
  ```

  

## Admin Site

### Automatic admin interface

- 서버 관리자가 활용하기 위한 페이지
- Model class를 admin.py 등록하고 관리
- django.contrib.auth 모듈에서 제공
- record 생성 여부 확인에 유용, 직접 삽입 가능



### admin 생성

```
$ python manage.py createsuperuser
```

- '/admin'에서 관리자 페이지 로그인
- auth에 관련된 기본 테이블 미생성시 관리자 계정 생성 불가



### admin 등록

```python
# articles/admin.py
from django.contrib import admin
from .models import Article

admin.site.register(Article)
```

- admin.py는 관리자 사이트에서 Article 객체가 관리자 인터페이스가 존재 한다는것을 인식

```python
# articles/admin.py
from django.contrib import admin
from .models import Article

admin.site.register(Article)

class ArticleAdmin(admin.ModelAdmin):
	list_display = ('pk', 'title', 'content', 'created_at', 'updated_at',)
```

- list_display
  - models.py에서 정의한 속성의 값을 admin페이지에 출력하도록 설정

- 그 외 옵션은 공식문서에서 admin 검색하여 찾아보자



## +CRUD 작성

- template상속시 'base.html'에 해당하는 문서는 최외각에 templates 폴더 생성후 그 안에 작성
  - setting에서 TEMPLATES - 'DIRS'항목 [BASE_DIR/templates]

- app 마다 urls.py 생성시 include 사용

  - ```python
    #project파일 안의 urls.py
    from django.contrib import admin
    from django.urls import path, include
    
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('articles/',include('articles.urls')),
    ]
    
    ```

  - ```python
    #articles 라는 이름의 app 안의 urls.py
    from django.urls import path
    from . import views				#현재 폴더(app)에서 views를 가져오므로 from .
    
    app_name = 'articles'			#이름 공간 설정
    urlpatterns = [
        path('', views.index, name='index'),	#name은 articles:index 형식으로 url호출시
        path('new/', views.new, name='new'),
        path('create/',views.create, name='create'),
        path('<int:pk>/',views.detail, name='detail'),
        path('<int:pk>/delete/',views.delete, name='delete'),
        path('<int:pk>/edit/',views.edit, name='edit'),
        path('<int:pk>/update/',views.update, name="update"),
    ]
    ```

- redirect()

  - render방식으로 함수에서 다른 함수가 적용되는 html 이동시 context가 없기에 제대로 작동을 안함
  -  새 URL로 요청을 다시 보내는 방식
    - 전체 URL 자체를 재구성 

  ```python
  def what_redirect(request):
  	return redirect('articles:detail', article.pk)
  ```

  

- 특정 조건을 만족할때만 실행되게 (delete)

  ```python
  def delete(request, pk):
      article = Article.objects.get(pk=pk)
      if request.method == 'POST':
          article.delete()
          return redirect('articles:index')
      else:
          return redirect('articles:detail', article.pk)
  ```

  - POST 형식으로 왔을때만 delete 됨

- 수정 하고자 할때 그 전 값 보여주기

  ```django
  <label for="title">Title</label>
      <input type="text" name="title" value="{{ article.title }}">
      <br>
      <label for="content">Content: </label>
      <textarea name="content" cols="30" rows="5" {{ article.content }}></textarea>
      <br>
      <input type="submit">
  ```

  - value값을 주어 그 전 값을 보여줌
  - textarea의 경우 value 속성이 없어 태그 내부 값으로 작성