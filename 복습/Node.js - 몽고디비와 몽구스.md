이 글은 MongoDB와 Mongoose 패키지가 설치된 상태를 가정한다. MongoDB의 핵심 용어를 살펴보고, express에서 mongoose를 통해 간단한 CRUD 작업을 하는게 오늘의 학습 목표다. 자세하게 공부하면 끝도 없기 때문에, 어떤 식으로 동작하는지만 이해하고 나머지 기능은 프로젝트를 하면서 필요할때 찾아 쓰면 된다. 공식문서에 잘 나와있다. 

## **MongoDB 용어**

[##_Image|kage@T5ESw/btrB1ob2NRc/LE8DC2KrcBikA3anBdQwk1/img.jpg|CDM|1.3|{"originWidth":600,"originHeight":400,"style":"alignCenter","caption":"https://kciter.so/posts/about-mongodb"}_##]

RDBMS와 비교하는게 가장 직관적이어서 이 둘의 대응 관계를 나타내는 그림을 가져와봤다. 사진에 첨부한 링크로 가면 MongoDB에 대해 아주 상세하게 설명해놨으니 가서 보는 것도 추천한다. 위 그림과 같이, MongoDB는 크게 네 가지 요소로 이루어져 있다. 

**Database**

하나 이상의 collection을 가질 수 있는 저장소. Database는 collection들을 담는 컨테이너라고 생각하면 된다.

ex) 기숙사 Database

**Collection**

하나 이상의 Document가 저장되는 공간. 

ex) 학생, 직원, 물품, 방 등

**Document**

MongoDB에 저장되는 자료. 데이터 하나가 Document에 대응되는 것으로 생각하면 된다. 

ex) 학생 - '이름: 홍길동, 나이: 14세, 성별: 남'

      방   - '호수: 703, 수용인원: 3, 현재인원: 2'

**Field**

Document의 속성. 

ex) 이름, 나이, 성별, 호수 등

## **Mongoose 사용법**

Mongoose는 express에서 MongoDB를 쉽게 사용할 수 있게 하는 패키지다. 사용법은 크게 아래의 다섯 단계로 이루어진다.

> **1\. 스키마 생성**  
> **2\. MongoDB 연결(Database 바인딩)**  
> **3\. 모델 생성(Collection 바인딩)**  
> **4\. 생성한 모델로 CRUD 작업**

#### **1\. 스키마 생성**

Collection에 저장될 Document의 형식을 미리 정의하는 단계로, 데이터 생성 및 수정 작업 시 데이터 형식을 체크해주는 기능을 제공한다. timestamps 옵션을 사용하면 생성, 수정 시간을 자동으로 기록해 준다. 데이터 생성 시 스키마 속성들이 다 들어갈 필요는 없지만, 자료형이 다르면 오류가 발생한다. 

**스키마는 쉽게 말해서 바인딩할 Collection의 인터페이스라고 생각하면 된다**. 

```
const { Schema } = require('mongoose');

const PostSchema = new Schema({
  title: String,
  content: String
}, {
  timestamps: true
});

module.exports = PostSchema;
```

참고로 MongoDB에서 제공하는 자료형은 아래와 같다. 자세한 내용은 [여기](https://mongoosejs.com/docs/schematypes.html)를 참고하자.

```
String, Number, Date, Buffer, Boolean, Mixed, ObjectId, Array, Decimal128, Map
```

#### **2\. MongoDB 연결(Database 바인딩)**

```
const mongoose = require('mongoose');

mongoose.connect('mongodb://주소/DB이름') // 연결

mongoose.connection.on('connected', () =>{})       // 연결 완료 이벤트
mongoose.connection.on('disconnected', () =>{})    // 연결 끊김 이벤트
mongoose.connection.on('reconnected', () =>{})     // 재연결 이벤트
mongoose.connection.on('reconnectFailed', () =>{}) // 재연결 시도 횟수 초과 이벤트
```

connect 함수 내부에 연결할 주소를 적어주면 MongoDB에 연결된다. 주소는 MongoDB Compass를 사용한다면 좌측 위에 표시되어 있으니 그대로 쓰면 된다. **주소는 로컬 환경이라면 'localhost:27017'**일 것이다. **DB이름에는 말 그대로 연결하고 싶은 Database의 이름**을 적어주면 된다. connect에는 다양한 이벤트가 있는데 위의 이벤트를 잘 활용하면 도움이 될 것이다. 

#### **3\. 모델 생성(Collection 바인딩)**

1번에서 정의한 스키마를 mongoose에서 사용할 수 있는 모델로 만드는 과정이다. 여기서 **모델은 바인딩할 Collection**으로 생각하면 되고, **스키마는 모델의 인터페이스**로 생각하면 된다. 바인딩할 Collection의 이름은 model 함수의 인자에 적어주면 된다. 

```
const mongoose = require('mongoose');
const PostSchema = require('./schemas/board');

exports.Post = mongoose.model('Post', PostSchema);


/* 인자에 들어가는값 */
mongoose.model('Post', PostSchema);     // posts 컬렉션에 바인딩
mongoose.model('', PostSchema, 'Post'); // Post 컬렉션에 바인딩
```

다시 한번 말하지만 **모델 생성은 Database의 Collection에 바인딩하는 과정이다**. 위 코드에서 아래 두 줄에 주목하자. 첫째줄은 posts 컬렉션에 바인딩되고, 둘째줄은 Post 컬렉션에 바인딩된다. 

➝ 첫번째 인자는 모두 소문자 처리된 후 's'가 붙는다.  

➝ 세번째 인자는 그대로 반영된다. 첫번째 인자보다 우선순위가 높다.

#### **4\. 생성한 모델로 CRUD 작업**

3번에서 생성한 모델로 CRUD 작업을 수행하면 된다. 간단하게 어떤식으로 사용되는지만 알아본다. 자세한 내용은 [여기](https://mongoosejs.com/docs/queries.html)를 참고하자. Model.mothod() 의 결과는 Promise 객체라는 사실만 알아두자.

**CREATE 예시**

```
const { Post } = require('./models');

// 즉시실행함수 형태
(async () => {
  const created = await Post.create({
    title: 'first title',
    content: 'first content'
  }); // 단일 Document 생성
  
  const multipleCreated = await Post.create([
      item1,
      item2,
      item3
  ]); // 복수 Document 생성
})();
```

**READ 예시**

```
const { Post } = require('./models');

(async () => {
    const listPost = await Post.find(query);
    const onePost = await Post.findOne(query);
    const postById = awiat Post.findById(id);
})();
```

**UPDATE 예시**

```
const { Post } = require('./models');

(async () => {
  const updateResult = await Post.updateOne(query, {
    // 업데이터 내용. . .
  });

  const updateResults = await Post.updateMany(query, {
    // 업데이트 내용. . .
  })
})();
```

**DELETE 예시**

```
const { Post } = require('./models');

(async () => {
  const deleteResult = await Post.deleteOne(query);
  const deleteResults = await Post.deleteMany(query);
})();
```

**자주 사용되는 쿼리**

> \- { key: value } 형태로 매칭  
> \- $lt, $lte, $gt, $gte를 사용해 범위 설정  
>          ㄴ less than, greater than, e는 equal 을 뜻함  
> \- $in 으로 다중값 검색   
> \- $or 로 다중 조건 검색

```
Person.find({
    name: ['Charles', 'Ron', 'Wayne', 'Sony']
    // name: { $in: ['charles', 'Ron']} 와 동일
    ,
    age: {
    	$gte: 20,
        $lt: 30
    },
    languages: {
    	$in: ['ko', 'en']
    },
    $or: [
    	{ status: 'ACTIVE'},
        { isFresh: true }
    ]
});
```

추가 쿼리는 [Model](https://mongoosejs.com/docs/api/model.html), [Query](https://mongoosejs.com/docs/api/query.html)를 참고하길 바란다.  

<참조>

[https://kciter.so/posts/about-mongodb](https://kciter.so/posts/about-mongodb)    

[https://mongoosejs.com/docs/guide.html](https://mongoosejs.com/docs/guide.html)   

엘리스 강의자료