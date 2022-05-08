타입스크립트는 자바스크립트에 타입을 적용한 언어다. 타입을 추가하는 기본 방식은 아래와 같다. 

```
const/let/var 변수: 타입
```

### **기본 타입 ( js와 공통 )**

```
// 1. boolean
const bool: boolean = false;

// 2. number
const num: number = 6;

// 3. string
const str: string = "This is string";

// 4. null & undefined - 자기 자신의 타입만 할당 가능. void는 예외(아래쪽에 나옴)
const u: undefined = undefined;
const n: null = null;
```

위 타입들을 '원시타입'이라고 한다. 공식문서에 따르면 **원시타입을 선언과 동시에 할당할 때는 굳이 타입을 적어줄 필요가 없다**고 한다. 왜냐하면 변수의 타입은 해당 변수의 초기값의 타입을 바탕으로 추론되기 때문이다. 변수 선언만 하는 경우에는 당연히 타입을 적어주는 것이 좋다. 

**주의**해야할 점이 두가지 있다.

먼저, null 타입을 typeof로 체크해보면 'object'가 뜬다. 이는 자바스크립트 초기 버전의 버그라고 하니 기억해두자! 비록 typeof로 찍으면 'object'가 뜨지만, 실제 타입은 'null'임을 기억하자.

```
let a: object = {};
let b: null = null;

console.log(typeof a === typeof b); // true
a = b; // Type 'null' is not assignable to type 'object'
```

다음으로 타입의 대소문자를 구분할 필요가 있다. string과 String은 다르다. 전자는 원시타입으로 오직 타입으로만 쓸 수 있고, 후자는 원시 래퍼 객체다(정확하게는 Function 객체).

```
// 원시 타입
string, number, bigint, boolean, null, undefined, symbol

// 원시 래퍼 객체
String, Number, BigInt, Boolean, Symbol

typeof(string); // Error
typeof(String); // function
```

### **추가 타입 (js에는 없음)**

**1\. any**

```
// 모든 타입을 허용한다.
// 컴파일러는 any 타입이 붙은 변수에 대해서는 타입 체크를 하지 않는다. 

let anyType: any = true;
anyType = 1;
anyType = "any type!";
anyType = {};
anyType = [];
```

**주의점!** any는 타입 값의 메소드를 호출할 때 타입 검사가 아예 수행되지 않는다. 아래 코드에서 'val2.length'은 오류 없이 출력된다. 

```
let val: number = 10;
console.log(val.length); // 컴파일 에러

let val2: any = 10;
console.log(typeof val2); // number
console.log(val2.length); // undefined 출력
```

-   any는 타입을 일단 무시하고 이후에 정확히 적고 싶을때, 또는 코드 작성 시점에 타입을 알 수 없을때 사용한다.
-   any는 강력하지만 남용하면 Typescript를 쓰는 이유가 없게 되므로 최대한 사용을 지양하는게 좋다.

**2\. unknown**

```
// 모든 타입을 허용하지만 어떤 타입에도 할당될 수 없다. 
let unknownType: unknown = 10;
unknownType = {};
unknownType = "string";

let a: string = unknownType; // Error
```

unknown 타입은 말 그대로 ‘어떤 타입인지 알 수 없음'을 나타낸다. 어떤 타입이든 할당 가능하지만 할당된 값의 타입은 알 수 없는 것으로 본다. 위 코드에서 마지막 줄을 주목하자. 비록 unknownType에 문자열이 할당되었지만 타입을 ‘unknown’으로 지정해서 타입이 ‘string’으로 명시된 a에 할당될 수 없는 것이다. 

이를 해결해주기 위해서는 타입 검사 과정을 거쳐야 한다. 아래 두 가지 방법이 있다. 참고로 아래의 과정을 'Type narrowing'이라고 한다. 

```
let unknownType: unknown = 'string';
let a: string = '';
let b: string = '';

// 방법1
if(typeof unknownType === 'string'){
  a = unknownType; // 불가능
}

// 방법2
b= unknownType as string;

console.log(a, b);
```

**any 와의 차이점?**

 1) Type narrowing을 거치기 전에는 메소드 사용 또는 연산 불가. 

 2) Type narrowing을 거치고 나서는 특정된 타입으로서 동작한다.

```
let unknownNum: unknown = 1;
let unknownStr: unknown = 'string';

// 1. Type narrowing 거치기 전
unknownNum + 1;    // Error - 연산 불가
unknownNum.length; // Error - 메소드 사용 불가
unknownStr.length; // Error - 메소드 사용 불가

// 2. Type narrowing 거친 후
if(typeof unknownNum === 'number') {
	console.log(unknownNum.length); // Error - number 타입에는 length 메소드 없음
}
```

**3\. void**

undefined 만을 값으로 가질 수 있는 타입. 반환값이 없는 함수의 타입을 표시할 때 사용한다.

```
function non(): void {
	console.log("no return");
	return; // undefined를 반환. return을 생략해도 undefined 자동 반환.
}
```

**4\. never**

아무런 값도 가질 수 없는 타입. 아직까지는 어느 상황에서 써야 하는지 잘 모르겠다. 우선은 아래와 같이 외우자.

-   함수 내부에서 에러를 throw 하는 경우
-   에러만 반환하는 경우

```
function error(message: string): never {
	throw new Error(message);
}

function fail(): never {
	return error("Failed!");
}
```

### **배열과 튜플**

**1\. 배열**

```
// 두 가지 문법
let ary1: number[] = [1,2,3];
let ary2: Array<number> = [1,2,3];


// 불변 배열 타입 - ReadonlyAarray
// 값을 수정할 수 없고, 다른 변수에 할당될 수 없다. 
const roArray: ReadonlyArray<string> = ["red", "green", "blue"];
const roArray: readonly string[] = ["red", "green", "blue"];
```

**2\. 튜플**

튜플은 원소의 수와 각 원소의 타입이 정확히 지정된 배열을 말한다. 아래 두 가지만 기억하면 된다. 주석 참고!

```
// 1) 초기화 할때는 반드시 원소의 개수와 타입이 일치해야 한다.
let t: [number, string] = [1, 'hi'];
let ro: readonly [number, string] = [2, 'hello']; // readonly 가능
t = [1];         // Error
t = ['hi', 1];   // Error
t = [2, 'hello']
console.log(Array.isArray(t)); // true


// 2) 초기화 이후부터는 일반 배열과 똑같이 작용한다. 단, 선언시 명시한 타입만 원소로 가질 수 있다.
t = [1, 'hi'];
t.pop(); // [1]
t.pop(); // []
t.push(1,2,3,'a') // [1,2,3,'a']
t.push([4]) // Error - 배열의 원소 타입은 number, string만 허용
console.log(Array.isArray(t)); // true
```

### **객체**

```
const student: {name: string, id: number} = {name: '홍길동', id: 1}

// 타입 구분자로 세미콜론 사용 가능 - student: {name: string; id: number;}
```

선택 속성 → 속성명 뒤에 '?'를 붙여 특정 속성이 존재하지 않을 수 있음을 표현할 수 있다.

```
const student: {name: string, id?: number} = {name: '홍길동'}
```

readonly 속성 → 속성명 앞에 ‘readonly’를 붙여 속성의 재할당을 막을 수 있다. const와 유사하다.

```
const student: {
	readonly name: string,
  id: number
} = { name: '홍길동', id: 1};

student.name = '동길홍'; // Error - name은 readonly
```

### **enum (열거)**

특정 값(상수)들의 집합을 저장하는 타입

enum은 그 자체로 객체이기도 하다. typeof로 찍어봐도 'object'로 나온다. 그냥 객체를 사용하는 것과 다른 점은 아래와 같다. 

> **1\. enum의 속성값은 수정 불가능하다.(전부 readonly)**  
> **2\. enum은 상항 리터럴 타입이 사용된다.**  
> **3\. enum의 속성값으로 문자열 또는 숫자만 허용된다.**

같은 종류를 나타나낸 여러 개의 숫자 혹은 문자열을 다루고자 할때 Enum을 쓰면 가독성이 훨씬 좋아진다. 아래는 enum 사용 예시다. 

```
enum Direction {
  East,
  West,
  South = 5,
  North
}

let east : Direction = Direction.East; // 0
let west : Direction = Direction.West; // 1
let south : Direction = Direction.South; // 5
let north : Direction = Direction.North; // 6
```

리터럴로 표기해주지 않으면 첫번째 원소가 자동으로 0이 된다. 그리고 중간에 리터럴 값을 지정해주면(South) 바로 다음 값은 '지정값 + 1'이 된다. 그 이후의 값들 또한 이전값에 1을 더한 값이 된다. 참고로 이건 쓰일지 잘 모르겠는데 enum의 속성값이 숫자인 경우 key에 대응하는 value가 자신의 인덱스가 된다. 그리고 Direction\[index\]는 key 값이 된다. 즉, 위 코드에서 'Direction\[5\] = South' 가 성립한다. 