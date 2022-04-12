## shortcut

- render()
- redirect()

- get_object_or_404  => 해당하는게 없으면 404 error 형태로 에러 출력

```python
article = Article.objects.get(pk = pk)
```

```python
article = get_object_or_404(Article,pk=pk)
```

- get_list_or_404
  - API로 서버 쓸때 사용
  - 내용이 없을 경우 404 error가 발생됨
  - ex) JSON파일 받아서 출력을 하는데, JSON 내용이 없을경우



## Decorator

- 원본을 수정 하지 않고 함수에 기능을 추가하고 싶을 때 사용



### require_http_methods(ls)

```python
@require_http_methods(['GET','POST'])
def create(require):
    ~~~
```

- view함수에서 list 형태로 list 요소에 맞는 method만 받음, 해당하지 않는 method받을 경우 405 error



### require_POST

- view함수에서 'POST' method만 받음



### require_safe

- view함수에서 'GET' , 'HEAD' method만 허용



## Media file

- **사용자**가 웹에서 업로드 하는 정적 파일

### model field

#### ImageField()

- 이미지 업로드에 사용하는 모델 필드
- FileField를 상속받는 서브 클래스 => FileField 속성,메서드 사용 가능
  - 업로드된 객체가 유효한 이미지인지 검사
- ImageField는 주소가 문자열로 db에 저장됨(기본 주소 100자 제한, max_length로 변경 가능)

#### FileField()

- 파일 업로드에 사용하는 모델 필드
- upload_to // storage 두개의 선택인자



##### upload_to

- 업로드 디렉토리와 파일 이름을 설정하는 2가지 방법 제공
  - 문자열 값이나 경로 지정
  - 함수 호출 

- upload_to='images/~~~'
  - 실제 이미지가 저장되는 경로를 지정
- blank=True
  - 이미지 필드에 빈 값이 허용되도록 설정(선택적으로 업로드 하게)

```python
class Article(modles.Model):
    image= models.ImageField(upload_to'주소',blank=True)
```

- 사용을 위해선 추가 작업 필요

#### Model field option

1. blank

   - 기본값 False
   - True인 경우 필드를 비워 둘 수 있음
   - 유효성 검사에서 사용

2. null

   - 기본값 False
   - True인 경우 django가 빈 값에 대해 DB에 NULL로 저장
   - 문자열 기반 필드에는 사용 자제
     - no data에 대해 빈문자열('')과 'NULL' 두가지  값이 생김

   - DB에서 사용
     - null만으로는 유효성 검사를 통과 할 순 없음



### Media 파일 사용하기

1. MEDIA_ROOT 설정
   - setting.py 에서 `MEDIA_ROOT = BASE_DIR / 'media'`
2. MEDIA_URL 설정
   - MEDIA_ROOT에서 제공되는 미디어를 처리하는 URL
   - 업로드 된 파일의 주소(URL)를 만드는 역할
   - setting.py 에서 `MEDIA_URL = '/media/'`

3. urls.py에 코드 추가(공식문서 참고하자)(project의 urls.py)

   ```python
   from django.conf import settings
   from django.conf.urls.static import static
   
   urlpatterns = [
   	~~~
   ] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
   
   ```

4.  migrate를 위해 Pillow 라이브러리 설치 필요
   - pip install Pillow

5. html 문서에서 form태그에서 enctype="multipart/form-data" 추가

6.  Image 등의 file의 경우 'POST'가 아니라 views.py 함수에서 request.FILES 추가

   - ```python
     form = Article(request.POST, request.FILES)
     ```

7. html에서 표현하기 위해 `<img src="{{ article.image.url }}" alt = "{{ article.image }}">` 추가
   - if문으로 사용 안하면 없을경우 error 발생



#### 이미지 수정

- 하나의 데이터 덩어리이기 때문에 덮어 씌우는 형태로 수정됨

#### 이미지 Resizing

- img 태그에서 width, height 속성 사용
  - db에 무리 가는건 막을수 없음
- `pip install django-imagekit`
  - settings.py에 INSTALLED_APPS에 `imagekit'추가
  - 자세한건 공식 문서(django-imagekit) 찾아보자