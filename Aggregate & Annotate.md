## Aggregate & Annotate

## Aggregate(전체)

`https://docs.djangoproject.com/en/3.2/topics/db/aggregation/#aggregation`

- 종합,집합,합계 등을 뜻하는 단어
- 집계 함수(Sum, Avg, Max, Min, Count)등을 사용할 때 사용하는 메서드
- 집계 함수를 파라미터로 받아서 딕셔너리로 반환

```python
Book.objects.all().aggregate(Avg('price'))
```



## Annotate(Group By)

- 주석을 달다 라는 의미
  - SQL의 GROUP BY

- 필드를 하나 생성하여 내용을 넣는 형식
- 데이터베이스에 영향을 주지 않고, Queryset 컬럼 하나를 추가하는 형식
- Queryset 반환

```python
Article.objects.annotate(
    total_count=Count('comment'),
    pub_date=Count('comment', filter=Q(comment__created_at__lte='2000-01-01'))
)
```

