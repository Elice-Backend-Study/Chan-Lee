### **모듈?**

\- 모듈이란 독립적인 소프트웨어를 말한다.

\- 자바스크립트에서는 파일 하나하나가 모듈이다. 

\- 모듈은 자신만의 독립적인 스코프를 가져서 전역변수의 중복 문제가 발생하지 않는다.

\- 모듈은 **module.exports** 또는 **exports 객체**를 통해 외부로 공개한다.

\- 외부에서는 require 함수로 모듈을 사용할 수 있다. 

**참고!**

브라우저(클라이언트)에서 여러개의 script tag를 불러오면 하나의 파일로 병합해 동일한 유효범위를 갖는다. 전역 충돌 발생 가능.

### **module.exports  vs  exports**

\- 공식 문서에 따르면 **exports는 module.exports를 참조한다**.  

\- 이 둘은 'key: value' 형태면 항상 같다고 할 수 있다.

\- 하지만 'key: value' 형태가 아니면 참조 관계는 깨진다. 

\- 기본값은 {}로, 객체다. 

**1\. 초기에는 빈 객체, 둘 다 동일 객체 참조**

```
module.exports === exports  // true
module.exports // {}
exports        // {}
```

**2\. key-value 형태면 둘은 참조 관계다.**

```
exports.a = 'a';
module.exports.b = 'b';
console.log(module.exports) // { a: 'a', b: 'b' }
console.log(exports)        // { a: 'a', b: 'b' }
```

**3\. key-value 형태가 아니면 참조 관계 깨진다.** 

```
// Case1
exports = 1;        // 그냥 1을 담고 있는 변수
module.exports      // {}

// Case2
exports;            // {}
module.exports = 1; // 그냥 1을 담고 있는 변수
```

### **require**

\- require는 **module.exports를 반환**한다. 

\- module.exports가 함수든, 변수든, 키값 쌍이든 그대로 반환한다. 

**require 동작 방식**

```
1. require('foo') 형태

- 현재 디렉토리로부터 가장 가까운 상위 디렉토리의 /node_modules에서 foo 모듈을 찾는다. 
- 없으면 다시 상위 디렉토리의 /node_modules에서 foo 모듈을 찾는다.
- 루트에 도달할때까지 찾는다. foo 모듈 없으면 throw Error.


2. require('./foo') 형태

- foo 라는 파일을 찾는다.
- 못찾으면 foo.js 있는지 확인
- 못찾으면 foo.json 있는지 확인
- 못찾으면 foo.node 있는지 확인
- foo가 디렉터리면 './foo/index.js' 파일 로드
```

<참조>

[https://nodejs.org/api/modules.html#modules\_exports](https://nodejs.org/api/modules.html#modules_exports)   

[https://nodejs.sideeffect.kr/docs/v0.10.7/api/modules.html#modules\_file\_modules](https://nodejs.sideeffect.kr/docs/v0.10.7/api/modules.html#modules_file_modules)   

[https://www.zerocho.com/category/NodeJS/post/5835b500373b5b0018a81a10](https://www.zerocho.com/category/NodeJS/post/5835b500373b5b0018a81a10)   

[https://poiemaweb.com/nodejs-module](https://poiemaweb.com/nodejs-module)