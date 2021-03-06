---
layout: post
title:  promise 와 callback
date:   2017-08-25
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: post-1.jpg
tags: [Blog, Javascript, Development]
author: wonchangkim # Add name author (optional)
---

> ## promise

- 복잡한 비동기작업을 할때 진짜 편하다.
- 콜백을 미리 붙여놓고 값이 들어 왔을때 콜백(.then)실행한다.
- then 
    - then 자체가 promise 반환
    - 콜백안에서 promise를 반환하면 아래쪽 then promise반환값을 사용
- promise를 사용하면 비동기 작업을 연이어서 하는것이 쉬워진다.

```javascript
// Promise 객체를 반환하는 함수
function delay(ms) {
  return new Promise(resolve => {
    setTimeout(() => {
      console.log(`${ms} 밀리초가 지났습니다.`);
      resolve(); // promise에 undefiend 가 채워진다.
    }, ms);
  });
}
delay(1000)
  .then(() => delay(2000))
  .then(() => Promise.resolve('끝'))
  .then(console.log);

console.log('시작');
```

### fetch(최신버전 브라우저)
- fetch함수의 결과는 promise. response라는 객체가 들어간다.

```js
const API_URL = 'https://api.github.com';

fetch(`${API_URL} /repos/facebookincubator/create-react-app/issues?per_page=10`) // 결과는 promise
  .then(res => res.json()) //json 파싱,이것도 promise이다.
  .then(issues => {
    console.log('최근 10개의 이슈:');
    issues
      .map(issue => issue.title)
      .forEach(title => console.log(title));
    console.log('출력이 끝났습니다.');
  });
```

- Promise.all(); 진짜 많이 쓰임 중요
    - 대부분 배열이지만 배열이 아닐수 있다. iterable.
    - promise 가 여러개 있을때 promise에 값이 다 있을때 먼가 하고싶다.
    - promise.all();을 호출하면 새로운 promise가 만들어지고.위에 promise가 값이 다 담기면 배열에 값을 담아 새로운 promise를 반환한다.

## 비동기 함수(Async Function)

### Async
- 동기식 구조와 거의 똑같은 구조로 비동기 구조를 만들수있다.
- function() 앞에 async 를 붙이면된다.
- 화살표앞에 메소드 앞에.
- then 처럼 항상 promise를 반환한다.

### Awiat
- awiat 키워드 뒤에 promise가 결과 값을 가질때까지 비동기 함수의 실행을 중단시킨다.
- await 에서 뭄추는것은 비동기 식으로 멈추는것이다 .다른작업계속한다는 뜻
- 뒤에 오는 promise의 결과값을 result(변수) 에 대입 할 수 있다. 
- then 에는 매개 변수를 쓴다.

```js
async function main() {
  await delay(1000);
  await delay(2000);
  const result = await Promise.resolve('끝'); //변수를 쓴다.
  console.log(result);
}
main();
```

```js
function main2(){
    delay(1000)
    .then(()=> delay(2000))
    .then(() => Promise.resolve('끝'))
    .then(result => { //매개변수를썻다.
        console.log(result);
    })
}
```

- 복잡한 비동기 데이터 흐름을 아주 쉽게 표현할 수 있다.
- 비동기 함수 역시 Promise사용한다.
- await 키워드는 async 안에서 만 쓸 수 있다.

### Generator
- await 있는 자리에 yield 
- co 라이브러리 제너레이터를 사용해서 실행.
- async 가 새기기 전에 사용
- async await 와 거의 똑같음 : 통신이 끝나면 복구. 함수 멈췄다가 다시 실행. 이것(코드의 실행흐름)은 자바스크립트 엔진 아라서한다. 프로그래머가 관여못한다.
- 제너레이터는 next메소드를 쓰면 위 동작을 제어가능하다.(장점)
- TDD(test driven development) 엄밀하게 코드를 검사해야 되는 경우
- redux-saga 는 제너레이터 활용한다.

## 폼 이벤트
 ### change (check,기타 다른)
  - 입력할때는 안생기고 다른곳으로 포커스가 이동하면 change 이벤트 발생
 ### input (text를 입력하는 요소)

 - 키보드를 누를때 마다 이밴드 발생
 [https://codepen.io/wonchangkim/pen/goxBBo?editors=1011]
[https://codepen.io/wonchangkim/pen/XVaxwb]
 - contenteditable

 ### focus
  - focus 되었을떼

 ### blur
  - focus 가 없어졌을때

### form (정보를 어딘가에 보내기위해)

```js
<form method="GET" action="/form">
  <input type="text" name="id"> //name 객체의 속성의 이름이라고 생각해라
  <input type="submit">
</form>
```

 - method="" : GET,POST 
 - action="" : /form 을쓰면 로컬주소의 /form에 보낸다.
- httpbin.org
  - get : args (http://httpbin.org/get?a=1&b=2)
  
```js
<form method="get" action="https://httpbin.org/get"> //실제 서버주소
  <input type="text" name="id">
  <input type="password" name="password">
  <input type="submit" value="전송">
</form>
```

- 개발자도구 -> network (전송버튼누르고확인)
- submit은 form태그에 거는 이벤트이다. 제출직전에 검사할 수있다.
- 빈문자열을 입력할 경우 검사하는 법

```js
document.querySelector('form').addEventListener('submit',e =>{
  const elements = e.currentTarget.elements
  // if(elements.id.value === '' || elements.password.value  === ''){
    if(!elements.id.value  || !elements.password.value){
    alert('id와 password를 입력하세요') ;
    console.log('ddd');
    e.preventDefault();
  }
})
```
## 마우스 이벤트
### click, dbclick
### mouseover/mouseout // 들어갈때 나올때
### mousedown/mouseup
### mousemove

## 키보드이벤트
### keydown //키누를때
### keyup //키땔때

## 스크롤 이벤트
### scroll
- parallax scroll
- e 이벤트객체에는 정보가 없다.
- window.scrollY (정보가 담겨있다.)

## firebase
[https://github.com/fds7/fds-firebase-auth]
- 백엔드가 필요한 부분을 편하게 쓸수있다.

### 인증
- 로그인을 할수있게 하는 서비스(구글,페이스북,전화번호로그인,이메일/비밀번호)
- 아이디찾기,비밀번호찾기
- OAuth 자원서버와 인증서버 달라도 상호인증 할 수있게 하는것.
- Authorization callback URL : 리다이랙트. 깃헙에서 파이어베이스로 보내는것
- github REST API
  - api를 쓰면서 인증 절차가 필요하다.
  - token 내가 누구인지 밝혀주는 도구 (아이디와 비밀번호)
  - [https://developer.github.com/v3/#authentication] : curl 통신프로그램

### 실시간 데이터 서비스
- 객체나 배열을 통째로 저장하는 서비스
- JSON 트리(큰 JSON 통, 큰 객체)
- 실시간 업데이트 감지
- 규칙 설정을 토한 데이터 보안 (다이어리서비스: 내 데이터는 나만보기가능- 인증서비스와 연동가능) 
- 배열은 지원하지 않는다. 객체만 지원
- 복잡한 쿼리는 지원하지 않는다(firestore 사용.). 단순단 정렬
- 1번 접속으로 노드를 선택적으로 가져올수 없다.
- 1번 접속은 비용이 많이 듣다.(시간)
- 접속횟수를 줄이는것이 중요하다.

```js
//규칙정하기
{
  "rules": {
    ".read": "auth != null",  //로그인을 하면 읽을수 있다. 로그인안하면 null
    ".write": "auth != null" 
        "message":{
        ".read": "auth != null",  
        ".write": "auth != null"
        } 
  }
}
```

```js
//정규화(normalize) : 데이터를 만들어서 관계를 정해주는것.
"rooms":{
  "room1":{
    "title":"안녕하세요"
  }
}
//또다른 큰객체생성
"messages":{
  "won-20171112112005":{
    "roomId":"room1" //관계를 정해준다. entity
  }
}
```
-쓰기

```js
function writeUserData(userId, name, email, imageUrl) {
  firebase.database().ref('users/' + userId).set({ //루트노트의 userId위치. set() 위치에 값저장
    username: name,
    email: email,
    profile_picture : imageUrl
  });
}
```

```js
firebase.database().ref('users/wochangkim').set({address: 'seoul',job:'frontend'})
//promise 반환
```

```js
//이벤트 수신대기
var starCountRef = firebase.database().ref('posts/' + postId + '/starCount');
starCountRef.on('value', function(snapshot) { // 데이타 변경될때마다 콜백이 발생. on()메소드.
  updateStarCount(postElement, snapshot.val());
});
//데이터 한번읽기
var userId = firebase.auth().currentUser.uid;
return firebase.database().ref('/users/' + userId).once('value').then(function(snapshot) { //once()메소드
  var username = (snapshot.val() && snapshot.val().username) || 'Anonymous'; 
  // ...
});
```

```js
snapshot = await firebase.database().ref('users/wochangkim').once('value')
snapshot.val();
//{address: "seoul", job: "frontend"}
//set(); 통째로 노드가 바뀐다. 
//update({address:"jeju"}); // 일부만 바꾸고싶을때
```

-데이터삭제 : remove();
-push() : 데이터를 넣으면서 식별자 자동생성 메소드(동적으로생성되는 데이터: 할일관리에서 할일 목록.어떤데이터가 예상안되는 여러데이터일때)
-push() 하고 식별자를 확일할 필요가 있을때.

```js
ref = await firebase.database().ref('messages').push({
content:'안녕하세요', roomId:1})
data = await ref.once('value')
date.val() //{content: "안녕하세요", roomId: 1}
ref.path //저장된 위치 보여줌.
```

```js
   document.querySelector('.new-todo').addEventListener('keydown', async e => {
      if (e.key === 'Enter') {
        //입력 필드에 들어있는 값을 파이어베이스에 전송
         await firebase.database().ref('todos').push({ //await를 기다렸다가 push되면 및에 e.currentTarget.value = ''; 실행된다.
           //promise 리턴한다.
          content: e.currentTarget.value
        })
        e.currentTarget.value = '';
        //입력 필드를 비워주기
      }
    })
```
### 클라우드 저장소



