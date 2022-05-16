### **1\. 라우팅(Routing)**

> 라우팅은 URI(또는 경로) 및 특정한 HTTP 요청 메소드(GET, POST 등)인 특정 엔드포인트에 대한 클라이언트 요청에 애플리케이션이 응답하는 방법을 결정하는 것을 말합니다.

Express 공식문서에서 그대로 가져온 문장이다. 영어 번역본이라 그런지 문장이 매끄럽지 못하다고 느껴서 내가 두 파트로 나눠봤다.

**1) 클라이언트 요청은 URI + HTTP 메소드로 이루어진다.**

네이버 루트에 접속하는 경우 → 'https://www.naver.com/'(URI)  +  Enter(HTTP GET)

**2) 라우팅은 클라이언트 요청에 응답하는 방법을 말한다.** 

클라이언트 요청은 URI와 HTTP 메소드로 이루어진다고 했다. 그런데 요청에 대한 응답을 해주기 위해서는 URI가 어떤 구조로, 어떤 메소드로 요청이 올지 서버쪽에서 미리 정의해둬야 한다. 예를 들어 '/users 구조의 URI가 get 메소드로 올 때는 users.html을 보내주자'와 같은 규칙들을 미리 짜놔야 한다. 다행스럽게도 Express에는 이를 간단하게 처리해주는 함수가 존재한다.

**라우트 정의**

```
app.METHOD(PATH, HANDLER)
```

\- app은 express의 인스턴스

\- METHOD는 HTTP 요청 메소드

\- PATH는 서버의 경로 (URI 루트 하위)

\- HANDLER는 METHOD와 PATH가 일치할때 실행되는 함수

**\> 간단한 라우팅 예시**

```
app.get('/users', (req, res) => {
  res.send('this is users');
});
```

\- /users 라우트에 대한 GET 요청 응답은 클라이언트 화면에 'this is users'를 뿌려주는 것.

**\> HANDLER 두 개 순차 실행 예시**

```
app.get('/example/b', (req, res, next) => {
  console.log('the response will be sent by the next function ...');
  next(); // 반드시 next 호출해줘야 다음 HANDLER로 갈 수 있다.
}, function (req, res) {
  res.send('Hello from B!');
});
```

라우트에서 HANDLER는 두 개 이상 올 수 있다. HANDLER는 인자 순서로 차례대로 실행된다. 다음 HANDLER를 실행시키고 싶다면 반드시 HANDLER에 'next'라는 인자를 추가해주고 HANDLER 내부의 마지막 부분에 next를 호출해줘야 한다. HANDLER의 인자가 (req, res, next)인 함수를 '미들웨어'라고 하는데 이는 곧 다루니깐 우선 넘어가자.  

**\> app.route() - 라우트 경로에 다양한 메소드 적용 예시**

```
app.route('/books')
  .get(function(req, res) {
    res.send('Get a random book');
  })
  .post(function(req, res) {
    res.send('Add a book');
  })
  .put(function(req, res) {
    res.send('Update the book');
  });
```

\- /books 경로에 대해 get, post, put 요청이 왔을때를 한번에 표현 

\-  express.Router()를 통해 아래와 같이 모듈화 가능

**\> app.route() → express.Router() 모듈화 예시**

```
// ./routes/books.js
const { Router } = require('express');
const router = Router();

router.get('/', (req, res) => {
    res.send('Get a random book');
})

router.post('/',(req, res) => {
    res.send('Add a book');
})

router.put('/', (req, res) => {
    res.send('Update the book');
});

module.exports = router;
```

```
// ./app.js
const app = express();
const bookRouter = require('./routes/books');

app.use('/books', bookRouter);
```

\- 라우터 모듈(bookRouter)을 app.use 함수로 연결해서 사용 가능

\- '/books'로 들어오면 bookRouter로 넘겨서 처리

\- bookRouter 내부에서는 router로 이어받음. 경로가 이어진다고 생각하면 된다.

\- bookRouter 내부에서 get 요청에 대한 경로가 '/borrow' 이면 여기에 들어오는 요청 경로는 '/books/borrow'

**주의!** 

라우팅 경로 상에서 응답은 한번만 해야한다. 응답을 두 번 이상 하면 에러난다.

### **2\. 미들웨어(Middleware)**

> Q : 미들웨어가 뭔가요?  
> A : 인자가 req, res, next인 함수요

누군가 미들웨어의 정의를 묻거든 위처럼 답하길 바란다.. req, res는 각각 요청, 응답 객체고 next는 아주 특별한 함수다. 만약에 **next가 인자 없이 그냥 호출되면 이는 '다음 라우트로 이동'**이라는 뜻이고, **next 안에 인자가 있는 상태로 호출되면 바로 '에러 처리 미들웨어'로 이동**한다. **라우트 이동시 req, res 객체는 그대로 전달**된다.

미들웨어는 (req, res, next) 인자만 가진다. 그런데 에러처리 미들웨어만 특이하게 제일 앞에 err라는 인자가 추가된다. err의 정체는 next의 인자값이다. 

> ****미들웨어 구조**  
> **function(req, res, next) {}  
> **  
> 에러 처리 미들웨어 구조**  
> function(err, req, res, next) {}

**\> 미들웨어가 들어가는 위치는 일반적으로 아래와 같다.** 

```
// 모든 요청에 대해 적용
app.use(Middleware);


// 라우터에 적용
router.use(Middleware);


// METHOD, PATH에 맞는 요청일때만 적용
app.METHOD(PATH, Middleware[, Middleware[, Middleware ...]])
```

**\> 미들웨어를 사용하면...**

**1) 라우팅 순서를 제어할 수 있다.**

```
var express = require('express');
var app = express();

var myLogger = (req, res, next) => {
  console.log('LOGGED');
  next(); // 생략하면 밑에 get 실행 안됨
};

app.use(myLogger);

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(3000);
```

\- next() 를 생략하면 get 요청은 받을 수 없다. 

**2) req 값 조작이 가능하다.** 

```
function middleWare(req, res, next) {
    req.name = "charles";
    next(); // 필수
}

app.get('/user', middleWare, (req, res, next) => {
  console.log(req.name); // charles
  next();
});
```

\- '/user' 경로로 get 요청이 오면 middleWare를 거쳐 req에 name 속성이 생긴다. 

**3) 미들웨어 적용 순서는 중요할 수 있다.**

```
app.use(middleWare1);
app.use(middleWare2);
app.use(middleWare3);
```

\- 요청에 대한 미들웨어 적용 순서는 1-2-3

\- 서드파티 미들웨어를 사용할 경우, 선후관계에 따라 의미가 달라질 수 있다(그런 경우가 있었는데 기억 안남)  

**4) 에러 핸들링을 쉽게 할 수 있다.** 

```
...
next("Error");
...

app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```

\- next 안에 인자가 있으면 바로 에러 처리 미들웨어로 넘어간다. 

**<퀴즈>** - 미들웨어 실행순서

```
router.use(auth); // 3

router.get('/', (req, res, next) => { // 4
	res.send('Hello Router');
});

app.use((req, res, next) => { // 1
	console.log(`Request ${req.path}`);
    next();
});

app.use('/admin', router); // 2
```

<참조>

[https://expressjs.com/](https://expressjs.com/)