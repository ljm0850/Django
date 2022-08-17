# Class형 뷰

## urls.py

```python
from board import articles

urlpatterns = [
    path(
    	"api/v1/articles/",
    	articles.as_view({"get":"list","post":"create","delete":"destroy"})
        name = "articles"
    )
]
```

- **as_view** : 클래스로 진입하기 위한 진입 메소드
  - method가 'get'일 경우 articles Class의 list method가 작동



### views.py

```python
from rest_framework.viewsets import ModelViewSet
from .model import Article
class articles(ModelViewSet):
    articles = Article.object.all()
    def list(self,request):
        ~~~
        return Response(...)
   	def create(self,request):
        ~~~
        return Response(...)
    def destory(self,request,pk=None):
        ~~~
        return Response(...)
```

- https://www.django-rest-framework.org/api-guide/viewsets/