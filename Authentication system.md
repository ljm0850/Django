# Authentication system

## Authentication & Authorization

- Authentication(인증)
  - 신원 확인
- Authorization(권한)
  - 권한 부여
  - 인증된 사용자가 수행할 수 있는 작업을 결정
  - 클라이언트가 하고자 하는 작업이 허가된 작업인지를 확인



- 계정을 관리할 앱 이름은 가급적 accounts 로 생성
  - auth 관련된 기능들이 accounts를 기본 주소로 설정된 경우가 많음



## 쿠키와 세션 & 캐시

- 인증을 하기 위해 사용하는것이 쿠키& 세션



### HTTP

- Hyper Text Transfer Protocol
- HTML 문서 같은 리소스를 가져올 수 있도록 하는 프로토콜
- 웹에서 이루어 지는 모든 데이터 교환의 기초

#### HTTP 특징

- 비 연결 지향(connectionless)
  - 서버는 요청에 대한 응답을 보낸 후 연결을 끊음
  - 실시간 채팅,notion은 js 기능을 이용하는듯 하다.. (확실x)
- 무상태(stateless)
  - 연결을 끊는 순간 클라이언트와 서버 간의 통신이 끝남
  - 클라이언트와 서버가 주고 받는 메세지들은 서로 완전히 독립적
- 클라이언트와 서버의 지속적인 관계를 유지하기 위해 쿠키&세션이 존재



### 쿠키

- 서버가 사용자의 웹 브라우저에 전송하는 작은 데이터 조각
- 웹 페이지를 받을 때 정보를 쿠키에 담아 저장, 재 요청시 서버에 쿠키를 제공하여 응답을 받는데 사용
- 사용 목적
  - 세션관리
    - 로그인, 자동 완성, 장바구니 ...
  - 개인화
    - 크롬 다크모드 ...
  - 트래킹
    - 사용자 행동을 기록 및 분석

### 세션

- 사이트와 브라우저 사이의 상태 유지에 사용
- Django에서는 특정 session id 만을 포함하는 쿠키를 활용해서 브라우저와 사이트 사이 연결된 세션을 알아냄

### 캐시

- 이미지,css,js 파일을 브라우저or서버 단의 캐시 메모리에 저장하여 사용

- 빠른 시일 내에 다시 사용될 확률이 높은 정보를 저장하여 재요청시 서버를 거치지  않고 응답하는데 사용

  

### 쿠키 lifetime

1. Session cookies
   - 현재 세션 종료시 삭제
   - 브라우저가 현재 세션이 종료되는 시기를 정의
     - 세션 복원을 이용하여 끄더라도 지속되게 가능(오류로 인해 꺼졌을때 뜨던 창)
2. Persistent cookies(Permanaent cookies)
   - Expires 속성에 날자 혹은 Max-Age 속성에 지정된 기간이 지나면 삭제



### 미들웨어

- HTTP 요청과 응답 처리 중간에서 작동하는 시스템(hooks)
- HTTP 요청 -> Middleware -> URL -> view 
- 데이터 관리, 어플리케이션 서비스, 인증, API관리를 담당



----

## 로그인

#### AuthenticationForm

- 사용자 로그인 위한 form
- request 인자 필요

#### login 함수

- login(request, user, backend = None)

```python
from django.shortcuts import render,redirect
from django.contrib.auth import login
from django.contrib.auth.forms import AuthenticationForm
from django.views.decorators.http import require_http_methods

@require_http_methods(['GET','POST'])
def userlogin(request):
    if request.method =='POST':
        form = AuthenticationForm(request,request.POST)
        if form.is_valid():
            login(request, form.get_user())
            retrun redirect('accounts:index')
    else:
        form = AuthenticationForm()
    context = {
        'form':form,
    }
    return render(request, 'accounts/login.html', context)
```

#### get_user()

- AuthenticationForm의 인스턴스 메서드

  

## Authentication data in templates

- 별다른 불러오기 없어도 html 문서 내에서 {{ user }}로 현재 로그인된 유저 출력 가능
  - settings.py 에서 TEMPLATES에 이미 적용이 되있어서 가능



## 로그아웃

- Delete 와 비슷한 구조

#### logout 함수

- logout(request)

- HttpRequest 객체를 받고 반환 값은 없음
- 로그인 상태가 아니여도 오류 발생x
- session data를 DB에서 완전 삭제, 쿠키에서도 session_id 삭제

```python
from django.views.decorators.http import require_POST
from django.contrib.auth import logout

@require_POST
def userlogout(request):
    logout(request)
    return redirect('accounts:index')
```



## 로그인 사용자 접근제한

### is_authenticated

- User model의 속성
- 모든 User 인스턴스에 대해 True
  - AnonymousUser에 대해서만 False
- 권한 과는 관련이 없으며, 사용자가 활성화 상태or유효한 세션을 가지고 있는지 확인x

```python
views.py
def userlogin(request):
    if request.user.is_authenticated:
        return redirect

#.html
#{% if request.user.is_authenticated %}
```



### login_required (decorator)

- settings.LOGIN_URL에 설정된 절대 경로로 redirect

  - 기본 경로가 'accounts/login/'

- 사용자가 로그인 되어 있으면 정상적으로 view 함수를 실행

- 인증 성공 시 사용자가 redirect 되는 경로가 next라는 쿼리 문자열 매개 변수에 저장되어 실행됨

  - 글쓰기-로그인안되있으면 로그인창-바로 글쓰기로 넘어갈때 next경로에 저장된 것을 이용

  - 이 방법 이용시에는 html form action 값을 ""로 비워야 함

  - ```python
     return redirect(request.GET.get('next') or 'articles:index')
    ```

```python
@login_required
@require_http_methods(['GET', 'POST'])
def update(request):
```



## 회원가입

### UserCreationForm

- 주어진 username과 password로 권한이 없는 새 user 생성하는 ModelForm
- 3개의 필드
  - username
  - password1
  - password2

```python
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth import login as auth_login

@require_http_methods(['GET', 'POST'])
def signup(request):
    # 이미 로그인 되어 있으면
    if request.user.is_authenticated:
        return redirect('accounts:index')
    
    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            user = form.save()
            # 회원가입 이후 자동로그인 되게 하는 기능
            auth_login(request, user)
            return redirect('articles:index')
    else:
        form = UserCreationForm()
    context = {
        'form': form,
    }
    return render(request, 'accounts/signup.html', context)
```



## 회원탈퇴

```python
@require_POST
def delete(request):
    if request.user.is_authenticated:
        request.user.delete() #회원탈퇴
        auth_logout(request)	#로그아웃
    return redirect('articles:index')
```



## 회원정보 수정

### UserChangeForm

- 사용자의 정보 및 권한을 변경하기 위해 admin 인터페이스에서 사용되는 ModelForm

```python
# forms.py
from django.contrib.auth.forms import UserChangeForm
from django.contrib.auth import get_user_model

class CustomUserChangeForm(UserChangeForm):
    # password = None // 선택사항
    class Meta:
        model = get_user_model() # User
        fields = ('email', 'first_name', 'last_name',)
```

```python
from .forms import CustomUserChangeForm
from django.contrib.auth.forms import UserCreationForm, 
def update(request):
    if request.method == 'POST':
        form = CustomUserChangeForm(request.POST, instance=request.user)
        if form.is_valid():
            form.save()
            return redirect('articles:index')
    else:
        # 손대면 안되는 정보까지 건들이게됨
        #form = UserChangeForm(instance=request.user)
        form = CustomUserChangeForm(instance=request.user)
    context = {
        'form': form,
    }
    return render(request, 'accounts/update.html', context)
```



## 비밀번호 변경

### PasswordChangeForm

- 사용자가 비밀번호를 변경할 수 있도록 하는 Form
- 기본은 이전 비밀번호를 입력하여 비밀번호 변경 가능
  - 이전 비밀번호가 필요없는 SetPasswordForm을 상속받는 서브클래스
- UserChangeForm에서 안내하는 링크가 'accounts/password'



```python
from django.contrib.auth.forms import PasswordChangeForm
from django.contrib.auth import update_session_auth_hash
@login_required
@require_http_methods(['GET', 'POST'])
def change_password(request):
    if request.method == 'POST':
        form = PasswordChangeForm(request.user, request.POST)
        if form.is_valid():
            user = form.save()
            # 암호 변경 시 세션이 변경되어 자동 로그아웃됨, 이를 방지(업데이트)
            update_session_auth_hash(request, user)
            return redirect('articles:index')
    else:
        form = PasswordChangeForm(request.user)
    context = {
        'form': form,
    }
    return render(request, 'accounts/change_password.html', context)

```

