# Model relationship (M:N)

- 1:N 모델(ForeignKey)에서는 하나의 외래 키에 2개의 데이터를 넣을 수 없음(User - Articles 예시)
  - 따라서 같은 User가 다른 Articles와 관계가 또 있다면, 같은정보(이름,e-mail등의 정보)에 다른 ForeignKey값을 가진 객체를 추가 생성해야함
  - 데이터 양이 많아지면 중복자료가 너무 많아 **비효율적**, 이를 해결하기 위한 방안



## 1. 중개 모델

- User-Articles에서 ForeignKey를 정의하지 말고 새로운 모델을 만들어 중계 역할을 하게함

```python
class User(AbstractUser):
    pass

class Article(models.Model):
    #~~~
    pass

class Connect(models.Model):
    user = models.ForeignKey(AUTH_USER_MODEL, on_delete=models.CASCADE)
    arricle = models.ForeignKey(Article, on_delete=models.CASCADE)
```

- User와 Article에서 **각각 역참조**를 이용하여 조회하는 형식



## 2. ManyToManyField

- (M:N, many-to-many)관계 설정 시 사용하는 모델 필드

```python
class User(AbstractUser):
    pass

class Article(models.Model):
    user = models.ManyToManyField(AUTH_USER_MODEL)
    title = models.CharField(max_length=20)
```

- 자동으로 중개 모델을 만들어주는 형식
- **User에서는 역참조, Article에서는 참조**를 통해 조회
- 하나의 필수 위치인자가 필요
- 모델 필드의 RelatedManager를 사용하여 개체를 추가, 제거 가능
  - add() , remove(), ...



### related_name=' '

- 역참조시 사용할 manager의 이름을 설정하는 방법

```python
class User(AbstractUser):
    pass

class Article(models.Model):
    user = models.ManyToManyField(AUTH_USER_MODEL, related_name='article')
    title = models.CharField(max_length=20)
```

- User에서 역참조시 기존에 사용하던 article_set 대신 사용
  - user1.article.all()
  - 이제 article_set 형식으로는 사용 불가
- **ForeignKey,ManyToManyField 등 사용시 같은 모델을 참조하게 될 경우 article_id 형태로 생성되는 이름이 같기에 문제가 발생, 이를 해결하기 위한 방안**



### through

- 중계 테이블을 자동으로 생성하지 않고, 원하는 테이블로 **선택**하는 옵션

```python
class User(AbstractUser):
    pass

class Article(models.Model):
    user = models.ManyToManyField(AUTH_USER_MODEL, through='Connect')
    pass

class connect(models.Model):
    user = models.ForeignKey(AUTH_USER_MODEL, on_delete=models.CASCADE)
    arricle = models.ForeignKey(Article, on_delete=models.CASCADE)
    # 추가적인 커스텀
    problem = models.TextField()
    updated = models.DateTimeField(auto_now_add=True)
```



### symmetrical

- ManyToManyField가 **동일한 모델(자기 자신)**을 가리키는 정의에서만 사용
- sns의 팔로우// 팔로워 의 경우 일방적임
  - 이럴 경우 symmetrical=False 옵션을 줘서 구현

```python
class Person(models.Model):
    employer = models.ManyToManyField('self', symmetrical=False)
    name = models.CharField(max_length=20)
```



###  RelatedManager

#### add()

- 지정된 객체를 관련 객체 집합에 추가
- 이미 존재하면 복제x

```python
user1 = User.objects.create(name='ljm')
article1 = Article.objects.create(title='MN')

user1.article.add(article1)			# related_name 적용
== article1.user.add(user1)
```

#### remove()

- 관련 객체 집합에서 지정된 모델 객체 제거
- QuerySet.delete()를 사용하여 삭제되는 원리

```python
user1 = User.objects.get(pk=1)
article1 = Article.objects.get(pk=1)

user1.article.remove(article1)
== article1.user.remove(user1)
```





------

### QuerySet API - exists()

```python
if article.user.filter(pk=request.user.pk).exists()
# == if request.user in article.user.all()
```

- QuerySet에 결과가 포함되어 있으면 True 반환, 아니면 False

- 고유한 필드가 있는 모델이 QuerySet의 구성원인지 확인하는 가장 효율적인 방법