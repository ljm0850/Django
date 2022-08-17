# @property

- model 정의시 사용하여 메소드를 필드인것 처럼 사용할 수 있게 도와주는 역할

```python
# models.py
from typing import Optional
from django.db import models

class Status(models.TextChoices):
    NOT_YET = "NOT_YET", "고용 예정"
    EMPLOYEE = "EMPLOYEE", "고용중"
    FIRE = "FIRE", "해고"

class employee(models.Model):
    number = models.CharField(verbose_name= "등록 번호", editable = False)
    name = models.CharField(verbose_name="성함", max_length=20)
    ...
    
    @property
    def status(self):
        status_type: Optional[value] = 연산...
        if not status_type:
            return Status.NOT_YET
        if ....:
            return Status.EMPLOYEE
```

- Optional : 다른언어의 Nullable과 같은 기능

- model에서 employee class일 경우 (employee).status로 값 호출 가능