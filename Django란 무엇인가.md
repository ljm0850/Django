# Django 에 대하여

- Python Web framework중 하나

- Model-View-Controller
  - 디자인 패턴중 하나
  - Django 에서는 MTV라고 함
    - Model-Template-View

#### Model

- 데이터 구조를 정의, 데이터베이스의 기록을 관리

#### Template

- 파일의 구조, 레이아웃 정의
- 실제 내용을 보여주는데 사용

#### View

- HTTP 요청을 수신, HTTP 응답 반환
- model을 통해 데이터 접근
- template를 통해 응답의 서식을 받아 반환

## Web framework

### Static web page (정적 웹 페이지)

- **flat page**
- 서버에 미리 저장된 파일 그대로 전달되는 웹 페이지
  - 서버가 추가적인 처리 과정 없이 클라이언트에게 응답을 보냄
- 모든 상황에서 모든 사용자에게 동일한 정보를 표시
- HTML, CSS, JavaScrpt 로 작성되는 경우가 많음



### Dynamic web page(동적 웹 페이지)

- 서버가 추가적인 처리 과정 이후 클라이언트에게 응답을 보냄
  - 따라서 페이지 내용이 그때그때 다름
- Python, Java, C++ 등의 언어로 파일을 처리, 데이터 베이스와 상호작용