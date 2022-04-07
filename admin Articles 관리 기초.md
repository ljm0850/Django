# admin Articles 관리 기초

```python
from django.contrib import admin
from .models import Article
# Register your models here.

class ArticleAdmin(admin.ModelAdmin):
    list_display = ['pk', 'title', 'content', 'created_at', 'updated_at']

admin.site.register(Article,ArticleAdmin)
```

- 함수 정의하여 보여주고자 하는 data 표기
- register 를 통해 site 에 등록하여 관리



- 관리자 계정 만들기

```
$ python manage.py createsuperuser
```

