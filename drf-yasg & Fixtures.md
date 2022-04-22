`$ python manage.py dumpdata --indent 4 articles.article > articles.json`

- 각 앱에 fixtures 폴더 생성후 추가

`$ python manage.py loaddata articles.json comments.json (띄어쓰기를 이용하여 한꺼번에 여러 파일 불러오기 가능)`

--------

## REST API - M:N

### REST API 문서화(drf-yasg)

- API 설계 및 문서화에 도움되는 라이브러리

- `$ pip install drf-yasg` 
  - INSTALLED_APPS에 `drf_yasg`추가

- https://drf-yasg.readthedocs.io/en/stable/
  - 보고 urls.py 설정 추가



## Fixtures

- 앱 설정시 준비된 데이터로 DB를 채우고 싶을때 사용
- DB의 serialized된 내용이 포함됨
- 기본 경로 : app/fixtures/

### dumpdata

- DB의 데이터를 표준 출력으로 출력
- `$ python manage.py dumpdata --indent 4 data(auth.user 등) > data.json`
  - `-- indent 4`는 줄수를 나누기 위하여 작성, 작성 안해도 됨

### loaddata

- `python manage.py loaddata data.json`
  - 기본경로에 저장되어 있을시