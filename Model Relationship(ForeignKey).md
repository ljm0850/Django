# Model Relationship

## Foreign Key

- 한 테이블의 필드 중 다른 테이블의 행을 식별할 수 있는 키
- 참조하는 테이블의 외래키는 참조되는 테이블 1개에 대응됨
  - 존재하지 않는 행을 참조할 수 없음
  - list와 같은 형태로 여러개 참조 불가

### 특징

- 키를 사용하여 부모 테이블의 유일한 값(PK...) 참조
  - 참조 무결성

#### 데이터 무결성

##### 참조 무결성

- 데이터베이스 관계 모델에서 관련된 2개의 테이블 간의 일관성

##### 개체 무결성

- 모든 테이블이 PK를 가져야 하며, 고유한값, 빈 값은 허용하지 않는다

##### 도메인 무결성

- 정의된 형식에서 관계형 데이터베이스의 모든 컬럼이 선언되도록 규정



### ForeignKey field

- 참조하는 model class, on_delete옵션 필수

- 재귀`models.ForeignKey('self', on_delete_models.CASCADE)

```python
class Comment(models.Model):
    article = models.ForeignKey(Article, on_delete=models.CASCADE)
```

- Migration 시 articles_comment 테이블에 article_id 형태의 컬럼이 생김



#### on_delete

- 외래 키가 참조하는 객체가 삭제됬을 때 어떻게 처리할지를 정의
- CASCADE: 부모 객체가 삭제됬을 때 이를 참조하는 객체도 삭제
  - 나머지는 DOC에서 찾아보자



### 1:N 관계

#### 역참조('comment_set')

- 부모에 해당하는 Article 클래스에서 자식에 해당하는 인스턴스를 알기 위해 사용
- `article.comment_set.all()` 과 같은 형태로 사용

##### releated_name

```python
class Comment(models.Model):
    article = models.ForeignKey(Article, on_delete=models.CASCADE, releated_name='comments')
```

- `article.comment_set` 을 `article.comments` 로 바꿔 사용
  - migration을 다시 할 필요가 있음



#### 참조('article')

- models.py에서 정의한 것을 이용 
- `comment.article`과 같은 형태로 사용



---

## ForeignKey를 이용하여 댓글 만들기

### CREATE

1. forms.py 에서 CommentForm작성

```python
# articles/forms.py
from django import forms
from .models import Article, Comment
# ...
class CommentForm(forms.ModelForm):
    class Meta:
        model = Comment
        fields = ('content',) #__all__사용시 부모에 해당하는 Article을 선택하는 목록도 생김
```

2. views.py 수정

```python
from .forms import CommentForm
from .models import Comment		# 댓글 삭제에 사용
def detail(request, pk):
    article = get_object_or_404(Article, pk=pk)
    comment_form = CommentForm()
    comments =article.comment_set.all()		#READ에 사용
    context = {
        'article': article,
        'comment_form': comment_form,
        'comments' : comments,
    }
    return render(request, 'articles/detail.html', context)

```

3. html 수정

```django
  {% if request.user.is_authenticated %}	{# 로그인 시에만 가능 #}
  <form action="{% url 'articles:comments_create' article.pk %}" method="POST">
    {% csrf_token %}
    {{ comment_form }}
    <input type="submit">
  </form>
  {% else %}
  <a href="{% url 'accounts:login' %}">[댓글을 작성하려면 로그인 하세요.]</a>
  {% endif %}
```

4. urls.py 작성

```py
urlpatterns = [
path('<int:pk>/comments/',views.comments_create, name='comments_create'),
]
```

5. comments_create 함수 작성

```python
@require_POST
def comments_create(request,pk):
    if request.user.is_authenticated:		# 로그인 상태 확인
        article = get_object_or_404(Article, pk=pk)
        comment_form = CommentForm(request.POST)
        if comment_form.is_valid():
            comment = comment_form.save(commit=False)
            comment.article = article
            comment.user = request.user		#댓글 쓴 사람 이름 표기용
            comment.save()
        return redirect('articles:detail', article.pk)
    return redirect('accounts:login')

```

- save(commit=False)
  - 아직 데이터베이스에 저장되지 않은 인스턴스 반환
  - 저장전에 객체에 추가적인 작업이 필요시 사용



### READ

- detail에서 read에 필요한 함수는 위에서 작성

```html
{# detail.html #}
<h4>댓글 목록</h4>
  {% if comments %}
  <p><b>{{ comments|length }}개의 댓글이 있습니다.</b></p>
  {% endif %}
  <ul>
    {% for comment in comments %}
    <li>{{ comment.user }}-{{ comment.content }}	{# 작성자와 댓글 표기 #}
      <---------------------------></--------------------------->  
      {# 삭제 #}
      {% if user == comment.user %}		{# 현재 로그인 유저와 댓글 작성자가 같으면 #}
      <form action="{% url 'articles:comments_delete' article.pk comment.pk %}" 
            method = "POST" class="d-inline">
        {% csrf_token %}
        <input type="submit" value="삭제">
      </form>
      {% endif %}
    </li>
    {% empty %}
      <p>댓글이 없어요..</p>
    {% endfor %}
  </ul>
```



### DELETE

```python
#articles/urls.py
urlpatterns = [
    path('<int:article_pk>/comments/<int:comment_pk>/delete/', views.comments_delete, name='comments_delete'),
]
```

```python
# views.py
@require_POST
def comments_delete(request, article_pk, comment_pk):
    if request.user.is_authenticated:
        comment = get_object_or_404(Comment, pk= comment_pk)
        if request.user == comment.user:	#댓글쓴이와 현재 로그인 유저가 같으면
            comment.delete()
    return redirect('articles:detail', article_pk)
```



--------------------

##  Custom User model

### AUTH_USER_MODEL

- User를 나타내는데 사용하는 모델
- 'auth.User' 가 기본값
- 프로젝트 진행동안에는 변경 매우 힘듬
  - 왠만해서는 db & migrations를 날리고 하는게 빠름

1. model 작성

```python
# accounts/models.py
from django.contrib.auth.models import AbstractUser
#auth.User의 기본 클래스인 AbstractUser 상속
class User(AbstractUser):
    pass
```

2. settings.py에 추가하여 auth.User 대신 accounts.User를 인식

```python
# settings.py
AUTH_USER_MODEL = 'accounts.User'
```

3. admin site에 모델 등록

```python
#accounts/admin.py
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from .models import User

admin.site.register(User, UserAdmin)
```

4. 기존에 auth.User 사용하던 곳에서 accounts.User 사용하게 변경

```python
# accounts/forms.py
from django.contrib.auth.forms import UserChangeForm,UserCreationForm
from django.contrib.auth import get_user_model
class CustomUserChangeForm(UserChangeForm):
    class Meta:
        model = get_user_model()
        fields = ('email','first_name','last_name')
class CustomUserCreationForm(UserCreationForm):
    class Meta(UserCreationForm.Meta):
        model = get_user_model()
        fields = UserCreationForm.Meta.fields + ('custom_field',)
```

- views.py 함수 변경
  - UserCreationForm - > CustomUserCreationForm
  - UserChangeForm -> CustomUserChangeForm
- **get_user_model()**
  - User 클래스 직접 참조 대신 사용
  - 현재 프로젝트에서 활성화된 사용자 모델 반환
  - object 형태로 return



## User - Article (1:N)

- Article과 Comment 와 동일한 구조로 만듬

### settings.AUTH_USER_MODEL

- models.py 에서만 사용한다고 생각
- User 모델에 대한 외래 키 또는 다대다 관계를 정의 할 떄 사용
- 문자열로 return
- Django의 구동순서(INSTALLED_APPS 순서대로 앱 import , 각 앱의 model import..)에 의하여 get_user_model() 사용에 문제가 발생 할 수 있어서 사용



### User - Article & User-Comment (1:N)

1. model 변경 -> makemigrations , migrate

```python
from django.db import models
from django.conf import settings

class Article(models.Model):
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    
# User-Comment 에서 사용
class Comment(models.Model): 
    article = models.ForeignKey(Article, on_delete=models.CASCADE)
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
```

2. forms 변경(articles/forms.py), 

3. views.py 변경(create,delete<자기 글만 가능하게>,....)

```python
@require_http_methods(['GET', 'POST'])
def create(request):
    if request.method == 'POST':
        form = ArticleForm(data=request.POST)
        if form.is_valid():
            # article에 추가적으로 현재 로그인한 user 값 추가 필요
            article = form.save(commit=False)
            article.user = request.user
            article.save()
            return redirect('articles:detail', article.pk)
    else:
        form = ArticleForm()
    context = {
        'form': form,
    }
    return render(request, 'articles/create.html', context)

```

4. html 변경 (작성자가 누구인지 // 로그인한 사람이 작성자면 삭제버튼 보이게 ...)