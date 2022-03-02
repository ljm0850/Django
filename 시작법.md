# Django 시작

## 가상환경 설정

- 프로젝트 마다 환경을 동일하게 설정하기 위해 가상환경을 주로 사용함
  - 프로젝트마다 다르게 사용할순 없으니

### 프로젝트 생성

1. 가상환경 생성

   1. 가상환경 생성

      ```
      $ python -m venv ljm0850
      ```

      - python -m venv {가상환경 이름}

      

   2. 가상환경 활성화

   ```
   $ source ljm0850/Scripts/activate
   ```

   - source [가상환경 이름]/Scripts/activate
   - 비활성화는 $ deactivate



2. Django 설치

   ```
   $ pip install django=={version}
   ```

   - 가상환경에 Django  설치

   - version에 사용하고자 하는 버전입력
     - 버전을 입력하지 않으면 최신 버전으로 설치가 됨(22.3.2 기준 4.0)
     - 4.0은 LTS버전이 아님
     - LTS 버전을 사용하기 위해 3.2.12 를 일단 사용

   

3. Django 프로젝트 생성

   ```
   $ django-admin startproject ljm0850 .
   ```

   - django-admin startproject <프로젝트명> .
   - 프로젝트 이름에는 '-' 사용 불가



4. Python Interpreter 설정

   - vscode에서 터미널을 끄고 ctrl + shift + p
   - Python: Select Interpreter
   - 사용할 (가상)환경 설정

   

5. Django 서버 활성화

   ```
   $ python manage.py runserver
   ```

   - Django 프로젝트 생성시 manage.py 파일이 자동 생성됨



## 프로젝트 구조

#### __init__.py

- 디렉토리를 하나의 **패키지**처럼 다루게 하는 역할

#### asgi.py

- Asynchronous Server Gateway Interface
- Django 어플리케이션이 **비동기식 웹 서버**와 연결하는 역할

#### settings.py

- 어플리케이션의 설정 관리
- 프로젝트에서 앱을 사용하기 위해 INSTALLED_APPS에 등록해야함
  - app 생성 후 등록해야함
  - Local - Third party -Django 순서로 app 등록 하는것이 일반적

#### urls.py

- url과 views의 연결하는 역할
- HTTP 요청(request)을 view로 전달함

#### wsgi.py

- Web Server Gateway Interface
- Django의 어플리케이션과 웹서버를 연결하는 역할

#### manage.py

- Django 프로젝트와 상호작용하는 커맨드라인



## Application

### 생성

```python
$ python manage.py startapp articles
```

- $ python manage.py startapp <App이름>
  - 일반적으로 app이름은 복수형으로 만듬

### 구조

#### admin.py

- 관리자용 페이지 관리

#### apps.py

- 앱의 정보 관리

#### models.py

- 앱에서 사용하는 model 관리

#### tests.py

- 테스트 코드를 작성해서 테스트 하는 곳

#### views.py

- view 함수 관리

- HTTP 요청을 받아 HTTP 응답을 반환 하는 함수 작성

  ```python
  def functionname(request):
      context = {
          ...
      }
  	return render(request, 'test.html',context)
  ```

- Model을 통해 요청에 맞는 데이터 접근

- Template에 HTTP 응답 서식을 받아서 반환

#### templates

- template가 담긴 폴더
- HTML 등의 방식으로 파일의 구조 정의
- app 폴더 안에 기본 경로로 지정되어 있음