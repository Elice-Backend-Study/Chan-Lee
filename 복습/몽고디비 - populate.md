populate는 join과 유사한 개념이다. 우선 공식문서를 보면 populate를 아래와 같이 기술하고 있다. 

> Population is the process of automatically replacing the specified paths in the document with document(s) from other collection(s). 

**population은 document의 요소값을 다른 collection의 특정 document로 치환하는 과정**이다. 이해하기 쉽게 그림으로 보면 아래와 같다. 

[##_Image|kage@bk1gtu/btrCBMxUb90/8TsFwkqKkRDJpshi3j2isk/img.png|CDM|1.3|{"originWidth":844,"originHeight":238,"style":"alignCenter"}_##]

두 개의 collection(posts, users)가 있고, 좌우는 각각 하나의 document다. 좌측의 author 필드값을 보면 ObjectId로 되어 있는 것을 알 수 있다. 여기서 populate를 거치면 author 필드값이 우측 document로 '치환'된다. 

이렇게 **population은 참조 필드를 통해 이루어진다**. 참조 관계는 스키마 생성 단계에서 지정해주며, 참조 대상은 collection이다. 즉, 참조하는 collection에서 참조 필드의 ObjectId와 일치하는 document로 치환하는 것이다. 따라서 **스키마 생성 과정에서 특정 필드가 어떤 collection을 참조할 것인지 명시**해줘야 한다. 스키마 정의는 아래와 같다. 

```
// Post
const PostSchema = new Schema({
  title: String,
  content: String,
  author: {
    type: Schema.Types.ObjectId,
    ref: 'User' // 참조 collection 설정
  },
  comments: [CommentSchema]
});


// User
const UserSchema = new Schema({
  email: String,
  name: String,
  password: String
});


// Comment
const CommentSchema = new Schema({
  content: String,
  author: {
    type: Schema.Types.ObjectId,
    ref: 'User' // 참조 collection 설정
  }
});
```

여기서 관심 가져야 할 부분은 author 필드다. PostSchema의 author 필드가 의미하는 바는 아래와 같다.

```
author: {
    type: Schema.Types.ObjectId,
    ref: 'User' 
}
```

1\. author 필드는 User collection을 참조하고 있다.

2\. author의 값은 User 스키마를 따라야 하며, 저장되는 값은 User의 ObjectId 값이다. 

아래 예시를 보자. Post collection에 하나의 document를 생성하는 예시다. 

```
const author = await User.findOne({ name: 'charles'});

await Post.create({
    title: 'charles의 첫 포스팅',
    content: '안녕하세요, 반갑습니다',
    author: author
})
```

author 필드값은 User 스키마를 따라야 하므로 User collection에서 name 값이 'charles'인 document를 하나 뽑아왔다. 그리고 Post에 데이터 생성시 author 필드값에 User에서 뽑아온 데이터를 넣어줬다. 위 코드의 결과값을 출력하면 아래와 같다. 

```
{
    _id: new ObjectId("62861331c0befd77a7aeb494"),
    title: 'charles의 첫 포스팅',
    content: '안녕하세요, 반갑습니다',
    author: ObjectId("62860b7de1ea4769818828aa"),
    comments: Array,
    __v: 0
}
```

뽑아온 author는 객체임에도 불구하고 저장되는 값을 보면 ObjectId로 저장된 것을 알 수 있다. 이 ObjectId 값은 뽑아온 author의 ObjectId 값과 일치한다. 이렇게 저장되는 이유는 스키마 생성시 타입을 그렇게 지정해줬기 때문이다. 여기서 populate를 사용하면 author 필드값을 치환할 수 있다. 

```
await Post.findOne({ title: 'charles의 첫 포스팅'}).populate('author');

// 결과값
{
  _id: new ObjectId("62861331c0befd77a7aeb494"),
  title: 'charles의 첫 포스팅',
  content: '안녕하세요, 반갑습니다',
  author: {
    _id: new ObjectId("62860b7de1ea4769818828aa"),
    email: 'charles@test.com',
    name: 'charles',
    password: '123',
    __v: 0
  },
  comments: Array,
  __v: 0
}
```

이제 populate의 여러가지 활용법에 대해 알아보자. 스키마 정의는 아까와 동일하고, Post collection의 한 document는 아래와 같다. 여기서 치환 가능한 부분은 author 필드와, comments의 author 필드다.

```
{
  _id: new ObjectId("62861331c0befd77a7aeb494"),
  title: 'charles의 첫 포스팅',
  content: '안녕하세요, 반갑습니다',
  author: {
    _id: new ObjectId("62860b7de1ea4769818828aa"),
    email: 'charles@test.com',
    name: 'charles',
    password: '123',
    __v: 0
  },
  comments: [
    {
      content: 'ㅎㅇㅎㅇ',
      author: new ObjectId("62860b7de1ea4769818828ad"),
      _id: new ObjectId("6286157d7bc85f06902990f2")
    },
    {
      content: '잘 지내냐ㅋㅋㅋ',
      author: new ObjectId("62860c29f56133a243afe925"),
      _id: new ObjectId("628615a6ec87bdcc1926657b")
    }
  ],
  __v: 0
}
```

**1\. 전체 치환**

```
// 두가지 모두 가능
Post.findOne({ title: 'charles의 첫 포스팅'}).populate('author');
Post.findOne({ title: 'charles의 첫 포스팅'}).populate({ path: 'author' });
```

더보기

```
// 결과

{
  _id: new ObjectId("62861331c0befd77a7aeb494"),
  title: 'charles의 첫 포스팅',
  content: '안녕하세요, 반갑습니다',
  author: {
    _id: new ObjectId("62860b7de1ea4769818828aa"),
    email: 'charles@test.com',
    name: 'charles',
    password: '123',
    __v: 0
  },
  comments: [
    {
      content: 'ㅎㅇㅎㅇ',
      author: new ObjectId("62860b7de1ea4769818828ad"),
      _id: new ObjectId("6286157d7bc85f06902990f2")
    },
    {
      content: '잘 지내냐ㅋㅋㅋ',
      author: new ObjectId("62860c29f56133a243afe925"),
      _id: new ObjectId("628615a6ec87bdcc1926657b")
    }
  ],
  __v: 0
}
```

**2\. 역관계도 성립**

```
// 1번과 동일한 결과
const post = await Post.findOne({ title: 'charles의 첫 포스팅'});
    
await User.populate(post, {path: 'author'});
console.log(post);
```

**3\. 치환시 일부 필드만 가져오기**

```
Post.findOne({ title: 'charles의 첫 포스팅'}).populate('author', 'email name');

// populate({ path: 'author', select: 'email, name'}) 도 가능
// 두번째 인자로 원하는 필드만 공백으로 구분해서 적는다
```

더보기

```
// 결과

{
  _id: new ObjectId("62861331c0befd77a7aeb494"),
  title: 'charles의 첫 포스팅',
  content: '안녕하세요, 반갑습니다',
  author: {
    _id: new ObjectId("62860b7de1ea4769818828aa"),
    email: 'charles@test.com',
    name: 'charles'
  },
  comments: [
    {
      content: 'ㅎㅇㅎㅇ',
      author: new ObjectId("62860b7de1ea4769818828ad"),
      _id: new ObjectId("6286157d7bc85f06902990f2")
    },
    {
      content: '잘 지내냐ㅋㅋㅋ',
      author: new ObjectId("62860c29f56133a243afe925"),
      _id: new ObjectId("628615a6ec87bdcc1926657b")
    }
  ],
  __v: 0
}
```

4\. comments 내부의 author 치환법 - 역관계 활용

```
const post = await Post.findOne({ title: 'charles의 첫 포스팅'});
await User.populate(post.comments, { path: 'author' });

console.log(post.comments);
```

더보기

```
[
  {
    content: 'ㅎㅇㅎㅇ',
    author: {
      _id: new ObjectId("62860b7de1ea4769818828ad"),
      email: 'ron@test.com',
      name: 'ron',
      password: '123',
      __v: 0
    },
    _id: new ObjectId("6286157d7bc85f06902990f2")
  },
  {
    content: '잘 지내냐ㅋㅋㅋ',
    author: {
      _id: new ObjectId("62860c29f56133a243afe925"),
      email: 'sony@test.com',
      name: 'sony',
      password: '123',
      __v: 0
    },
    _id: new ObjectId("628615a6ec87bdcc1926657b")
  }
]
```

populate를 아직 완벽하게 이해했다고 할수는 없을 것 같다. 보충할 내용은 이후에 공부하면서 보충하는걸로...

<참조>

[https://mongoosejs.com/docs/populate.html#population](https://mongoosejs.com/docs/populate.html#population)   

[https://www.zerocho.com/category/MongoDB/post/59a66f8372262500184b5363]