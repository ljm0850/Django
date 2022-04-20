

# REST API

## HTTP

- HTTP Method
  - GET : 조회
  - POST : 작성
  - PUT : 수정
  - DELETE : 삭제
- HTTP response status code
  - informational responses (1~~)
  - Successful responses (2~~)
  - Redirection messages (3~~)
  - Client error responses (4~~)
  - Server error responses (5~~)



### URL, URN & URI

#### URL(Uniform Resource Locator)

- `ljm.com/index.html` 같은 형태
- 통합 자원 위치
- 네트워크 상에 자원이 어디 있는지 알려줌



#### URN(Uniform Resource Name)

- 통합 자원 이름
- URL과 달리 자원의 위치에 영향을 받지 않는 유일한 이름 역할

- 도서 번호 같은 곳에서 사용됨



#### URI(Uniform Resource Identifier)

- 통합 자원 식별자
- 인터넷의 자원을 식별하거나 이름을 저장하는데 사용되는 간단한 문자열
- URL,URN의 상위 개념



### URI 구조

#### Scheme (protocol)

- http, data, file, ftp, mailto

#### HOST(Domain name)

- 요청을 받는 웹 서버의 이름
- https://www.google.com/ 에서 www.google.com 에 해당
- IP address를 직접 사용 가능 (google=142.251.42.142)

#### Port

- 웹 서버 상의 리소스에 접근하는 기술적인 gate
- HTTP 표준 포트
  - HTTP 80
  - HTTPS 443

#### Path

- 웹 서버 상의 리소스 경로
- `https://www.google.com/search?q=URI` 에서 search 에 해당

#### Query (Identifier)

- ?q=URI 이후 부분에 해당

- & 로 구분되는 key-value 목록



#### Fragment

- Anchor
- 북마크와 같은 역할 (HTML 문서의 특정 부분을 보여주기 위한 방법)
- `#` 이후 주소로 표시됨, 서버에 요청을 보내진 않음

----

## RESTful API

### API

- Application Programming Interface
- 프로그래밍 언어가 제공하는 기능을 수행하게 만든 인터페이스
  - CLI 는 명령줄
  - GUI는 그래픽
  - API는 프로그래밍
- Web API
  - 웹 애플리케이션 개발에서 다른 서비스에 요청을 보내고 응답을 받기 위해 정의된 명세
- 데이터 타입
  - HTML, XML, JSON



### REST

- REpresentational State Transfer
- API Server를 개발하기 위한 일종의 소프트웨어 설계 방법론

- 자원을 정의하고, 자원에 대한 주소를 지정하는 방법



#### 방법

- 자원(정보)
  - URI
- 행위
  - HTTP Metohd(GET,POST,PUT,DELETE)
- 표현
  - 자원과 행위를 통해 표현된 결과물
  - JSON으로 표현된 데이터를 제공



#### JSON

- JavaScript Object Notation
- JavaScript의 표기법을 따른 단순 문자열
- 특징
  - 읽고,쓰기가 쉽고 기계가 파싱하고 만들어 내기 쉬움 (key:value 형태)
  - 파이썬의 dictionary, 자바스크립트의 object



-----

## Response

### Json 형태

```python
# views.py
from django.http.response import JsonResponse

def ljm(request):
    articles = Article.objects.all()
    articles_json = []
    for article in articles:
        articles_json.append({ 'key1' : value1,'ket2' : value2,} )
    return JsonResponse(articles_json, safe=True)
```

- safe
  - True가 기본값
  - dict 이외의 객체를 직렬화(Serialization)하려면 False

### 직렬화(Serialization)

- 데이터 구조 or 객체 상태를 동일(다른)컴퓨터 환경에 저장하고, 나중에 재구성이 가능하게 포맷하는 과정

- Django 에서는 Queryset, Model instance와 같은 데이터를 바로 JSON,XML 등의 형태로 바꿀수가 없기에 JSON,XML형태로 바꾸기 쉬운 Python 타입으로 만드는 과정

```python
from django.http.response import HttpResponse
from django.core import serializers

def ljm2(request):
    articles = Article.objects.all()
    data = serializers.serialize('json', articles)
    return HttpResponse(data, content_type='application/json')
```



### Django REST Framework(DRF)

- Django REST framework(DRF) 라이브러리를 사용한 JSON 응답

- `$ pip install djangorestframework`
  - INSTALLED_APPS 에 'rest_framework' 추가

```python
# articles/serializers.py
from rest_framework import serializers
from .models import Article

class ArticleSerializer(serializers.ModelSerializer):

    class Meta:
        model = Article
        fields = '__all__'
```

```python
# articles/views.py
from rest_framework.decorators import api_view
from rest_framework.response import Response
from .serializers import ArticleSerializer

@api_view()
def article_json_3(request):
    articles = Article.objects.all()
    serializer = ArticleSerializer(articles, many=True)
    return Response(serializer.data)
```

- many=True
  - 단일 인스턴스 대신 QuerySet 등을 직렬화 할때 사용

-----

## in Single Model

- 단일 모델의 data를 직렬화(serialization)하여 JSON 변환

- 단일 모델을 두고 CRUD 로직을 수행 가능하게 설계

### ModelSerializer

- 모델 정보에 맞춰 자동으로 필드 생성
- serializer에 대한 유효성 검사기를 자동으로 생성
- `.create()`& `.update()` 의 기본 구현이 포함됨



```python
from dataclasses import field
from rest_framework import serializers
from .models import Article

class ArticleListSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ('id','title')

class ArticleSerializer(serializers.ModelSerializer):

    class Meta:
        model = Article
        fields = '__all__'
```

```python
from django.shortcuts import render,get_object_or_404,get_list_or_404
from .models import Article
from .serializers import ArticleListSerializer, ArticleSerializer
from rest_framework.response import Response
from rest_framework.decorators import api_view
from rest_framework import status

@api_view(['GET','POST'])
def index(request):
    if request.method == 'GET':
        articles = get_list_or_404(Article)
        serializer = ArticleListSerializer(articles,many=True)
        return Response(serializer.data)
    
    elif request.method =='POST':
        serializer = ArticleSerializer(data=request.data)
        if serializer.is_valid(raise_exception=True):
            serializer.save()
            return Response(serializer.data,status=status.HTTP_201_CREATED)

@api_view(['GET','PUT','DELETE'])
def detail(request, article_pk):
    article = get_object_or_404(Article, pk=article_pk)
    if request.method == 'GET':
        serializer =  ArticleSerializer(article)
        return Response(serializer.data)
    elif request.method =='PUT':
        serializer = ArticleSerializer(article,request.data)
        if serializer.is_valid(raise_exception=True):
            serializer.save()
            return Response(serializer.data)
    elif request.method =='DELETE':
        article.delete()
        data = {
            'delete' : article_pk,
        }
        return Response(data)
```

#### api_view decorator

- default는 GET
- 허용된 메서드가 아니면 405 Method Not Allowed 응답

- DRF에서는 필수 작성

#### status=status.HTTP_201_CREATED

- DRF에는 status code를 보다 명확하고 읽기 쉽게 만드는 데 사용할 수 있는 상수 집합을 제공 
- **status** 모듈에 HTTP status code 집합이 모두 포함되있음



#### raise_exception=True

- **is_valid()** 유효성 검사에서 오류가 있을 경우 예외를 발생시킴
- 기본적으론 HTTP status code 400





### 1:N 관계

- save()를 하려면 추가적인 데이터를 줘야함(글-댓글관계에서 댓글은 글에 대한 정보가 필요)
- save(article=article) 형태로 () 안에 필요한 정보를 추가



#### Read Only Field(읽기 전용 필드)

- 위의 상황에서 어떤 게시글에 작성하는 댓글인지에 대한 정보를 form-data로 넘겨주지 않아서 직렬화 과정에서 is_valid를 통과하지 못하는 상황 발생
- 이때 read_only_fields를 통해 직렬화 하지 않고 반환 값에만 해당 필드가 포함되도록 설정 가능

```python
# articles/serializers.py

class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = '__all__'
        read_only_fields = ('article',)
```

```python
# articles/views.py
@api_view(['POST'])
def comment_create(request, article_pk):
    article = get_object_or_404(Article, pk=article_pk)
    serializer = CommentSerializer(data=request.data)
    if serializer.is_valid(raise_exception=True):
        serializer.save(article=article)
        return Response(serializer.data, status=status.HTTP_201_CREATED)
```

#### 추가적인 사항

```python

class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = '__all__'
        read_only_fields = ('article',)
        
class ArticleSerializer(serializers.ModelSerializer):
    comment_set = CommentSerializer(many=True, read_only=True)
    comment_count = serializers.IntegerField(source='comment_set.count', read_only=True)

    class Meta:
        model = Article
        fields = '__all__'
```

- CommentSerializer 와 ArticleSerializer의 순서가 바뀔 경우
  -  `comment_set = CommentSerializer(many=True, read_only=True)`
  - CommentSerializer 가 정의가 안되어 문제 발생



- `comment_count = serializers.IntegerField(source='comment_set.count', read_only=True)`

  - comment_set 필드는 모델 관계로 인해 자동 생성은 됨.
  - 별도의 값으로 사용하려면 위와 같이 직접 필드 작성해야함

  - `source`: 필드를 채우는 데 사용 할 속성의 이름, `.(dot)`을 통해 속성 탐색 가능
  - override 할 경우 `read_only_fields` 에 추가 불가능