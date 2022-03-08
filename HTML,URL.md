# HTML 데이터 입력,전송 // URL

## HTML 데이터 입력,전송

### form

```html
<form action="#" method="get" class="form-example">
	<div>
    	<label for="name">이름을 적어주세요: </label>
    	<input type="text" id = "name" name = "name">
    </div>
    <div>
    	<label for="pw">label</label>
    	<input type="password" id = "pw" name = "pw">
    </div>
    <input type="submit" value="Subscribe!">
</form>
```

- text, button, checkbox, file, ...을 제공
  - 받은 데이터를 서버로 전송



- **action**: 데이터를 받을 URL 지정
- **method**: 데이터 전달 방식 지정
  - get
  - post


#### input

```html
<input type="text" id="name" name="name" >
```

- 데이터를 입력 받기 위해 사용
  - 양식 제출시 name = "value" value를 넘김 
  
  

#### label

```html
<label for="name">Name: </label>
```

- 인터페이스 항목에 대한 설명을 함
- for // id 를 통해 input,button, textarea....와 연결

- label을 클릭해서 input에 초점을 맞추거나 활성화 가능



### HTTP

- HyperText Transfer Protocol
- 웹에서 일어나는 모든 데이터 교환의 기초
- request method
  - GET, POST, PUT ....

#### GET

- 서버로부터 정보를 조회 하는데 사용
- 데이터를 가져올 때만 사용해야 함
- DB에 영향 X

#### POST

- 서버로 데이터를 전송시 사용
- 서버에 변경사항을 만듦

- CSRF 사용
  - {{% csrf_token %}}

##### CSRF

- 사이트 간 요청 위조(Cross-site request forgery)

## URL

### Variable Routing

- URL 주소를 변수로 사용하는 것

- 변수 값에 따라 하나의 path()에 여러 페이지를 연결 가능

  ```django
  path('accounts/ljm0850/<int:user_id>/',...)
  ```

  - accounts/ljm0850/1 -> ljm0850 1번 페이지

### URL Path converters

- str
  - '/'를 제외한 모든 문자열
- int
  - 0 또는 양의 정수
- slug
  - 아스키코드 문자열