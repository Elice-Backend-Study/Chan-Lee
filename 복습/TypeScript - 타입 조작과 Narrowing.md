## **1\. 타입 조작**

**1) 유니언 타입**

**서로 다른 두 개 이상의 타입을 사용**하고자 할때 유니언 타입을 사용할 수 있다. 사용 방법은 굉장히 간단한데, 그냥 타입 사이에 | 를 넣어주면 된다. 

```
let tmp: string | number | boolean;
tmp = 'string';
tmp = 2;
tmp = true;
tmp = []; // Error
```

함수 매개변수에 적용할때 Type Narrowing을 통해 타입에 유요한 메서드를 사용할 수 있다.

```
function strORnum(ary: string | number) {
	console.log(ary.length); // Error: Property 'length' does not exist on type 'number'
}

// Type Narrowing으로 해결
function strORnum(ary: string | number) {
	if(typeof ary === "string") {
    	console.log(ary.length);
    } else {
    	console.log("No length method")
    }
}
```

참고로 배열인지 판별할 때는 Array.isArray() 메소드를 써야 정확하다.

**2) 타입 별칭**

객체 타입뿐 아니라 모든 타입에 대해 새로운 이름을 부여할 수 있다. 

```
// Case1
type ID = number | string;
let a: ID;
a = 1;
a = "string";


// Case2
type Point = {
    x: number;
    y: number; // 구분자로 ,를 써도 상관없다. 
}

function printCoord(pt: Point) {
	console.log(pt.x, pt.y);
}

printCoord({x: 1, y: 1});
```

**3) 인터페이스**

인터페이스는 객체 타입을 만드는 또 다른 방법이다. **객체 타입에만 적용 가능**하고, 원시 타입에 별칭을 부여하는 데에는 사용할 수 없다. 인터페이스는 내용이 꽤 많아서 나중에 자세히 정리하겠다. 우선 대략적인 내용만 살펴보자.

```
interface Point {
    x: number;
    y: number; // 구분자로 ,를 써도 상관없다
}

function printCoord(pt: Point) {
    console.log(pt.x, pt.y);
}

printCoodr({ x: 1, y: 1 });
```

**Index Signatures**

인터페이스 생성시 객체의 속성명을 다 모를때, 인덱스로 한꺼번에 표현하고 싶으면 아래와 같이 하면 된다. 단, 인덱스 타입은 숫자와 문자열만 가능하다. 

```
// 1) 숫자 인덱스
interface StringArray{
    [index: number]: string;
}

const obj: StringArray = {
    1: 'one',
    2: 'two',
    3: 'three'
}



// 2) 문자 인덱스
interface Dictionary{
    [index: string]: string;
}

const dict: Dictionary = {
  "cat": "meow",
  "dog": "wolf",
  "cow": "moo"
}
```

**type 과 interface의 차이는 '확장'에 있다!**

둘 다 확장 가능한 경우

```
// 1) 인터페이스 확장 - extends 키워드
interface Student{
  shcool: string;
}

interface Ron extends Student {
  name: string;
  id: number;
}

interface Ron extends interA, interB, interC {} // 다수 인터페이스 동시 확장 가능



// 2) 타입 확장 - &
type Student = {
  shcool: string;
}

type Ron  = Student & {
  name: string;
  id: number;
}
```

인터페이스만 확장 가능한 경우

```
// 1) 인터페이스
interface Student {
  id: number;
}

interface Student {
  name: string;
}



// 2) 타입 확장 불가
type Student = { // Error - Duplicate identifier 'Student'
  id: number;
}

type Student =  { // Error - Duplicate identifier 'Student'
  name: string;
}
```

인터페이스는 중복을 허용하지만 타입은 중복을 허용하지 않는다. 그리고 **인터페이스에서 이름이 중복될 경우 타입은 확장된다**. 즉, 위 코드에서 Student의 속성은 id, name이 된다. 

인터페이스와 타입 별칭의 차이점을 한 가지만 더 말하자면.. **타입 별칭은 실제로는 새 타입을 생성하지 않지만, 인터페이스는 실제로 새 타입을 생성**한다. 이게 무슨 말이냐면 **타입 에러**가 날때 표시되는 타입이 **타입 별칭은 별칭이 아닌 원시 타입이 표시**되지만 **인터페이스는 정의한 타입 이름이 표시**된다. 웬만하면 인터페이스를 사용하는 것이 좋다. 

**4) 타입 단언**

컴파일러에게 특정 타입의 사용을 강제하는 방법이다. 사실 아직까지는 필요성을 못느끼겠다. 프로젝트를 하다가 필요성을 느끼면 그때 다시 정리해도 늦지 않을 것 같다. 'as' 키워드 또는 '<>'로 적용할 수 있다. 

**5) 리터럴 타입**

string, number, boolean은 그 값 자체로 타입이 될 수 있다. 리터럴 타입은 유니언과 함께 사용하면 유용하다. 

문자 리터럴 타입 활용 예시

```
function printText(s: string, alignment: "left" | "right" | "center"){
	console.log(s, alignment);
}

printText("Hello World!", "left");
printText("Hello World!", "rihgt"); // Error: right 오타
```

숫자 리터럴 타입 활용 예시

```
function compare(a: number, b: number): -1 | 0 | 1 {
  return a === b ? 0 : a > b ? 1 : -1;
}

console.log(compare(1, 1)); // 0
console.log(compare(1, 2)); // -1
console.log(compare(2, 1)); // 1
```

## **2\. 타입 Narrowing**

타입 Narrowing은 말 그대로 타입을 좁히는 과정이다. 타입을 구체화시키는 과정으로 말하는게 더 정확할 것 같다. 타입을 구체화시키지 않으면 아래와 같은 실수를 할 수 있다. 

```
function padLeft(padding: number | string, input: string) {
	return " ".repeat(padding) + input; 
}
// Error: padding이 number인 경우 repeat() 적용 불가
```

위 코드는 오류를 발생시킨다. 이유는 padding의 타입이 number 또는 string으로 지정되어 있는데 number일 경우 repeat() 메소드를 적용시킬 수 없기 때문이다. 이를 해결하려면 아래와 같이 padding의 타입이 number일때와 string일때로 나눠서 처리해주면 된다.  

```
function padLeft(padding: number | string, input: string) {
  if (typeof padding === "number") {
    return " ".repeat(padding) + input;
  }
  return padding + input;
}
```

typeof를 통해 padding의 타입을 검사해주면 함수 내부에서 더이상 타입 에러는 발생하지 않는다. else 문을 써주지 않아도 되는 이유는 padding의 타입 선택지가 두 가지 뿐이라 number가 아니면 무조건 string이기 때문이다.

이처럼 **if문 내부에서 타입을 검사하는 과정을 타입 Narrowing이라고 한다. 타입 Narrowing 과정을 거치면 타입이 구체화되고, 컴파일러는 구체화된 타입을 채택**한다. 이제 타입 Narrowing을 하는 방법들을 살펴보자. 

**1) typeof operator**

typeof 연산자는 타입의 종류를 문자열로 반환한다. 반환값은 아래 8가지 값 중에 하나다. 

```
"string"
"number" 
"bigint"
"boolean"
"symbol"
"undefined"
"object"
"function"
```

원시 타입에 object, function이 추가된 정도다. 그런데 자세히 보면 'null'은 빠져있다. 이는 자바스크립트의 초기 버그라고 하는데, null을 typeof 연산자로 찍어보면 'object'가 나온다. 주의하자!

**2) Truthiness narrowing**

falsy를 사용해 타입을 좁히는 방법이다. falsy란 불리언 문맥에서 false 로 평가되는 값으로, 아래 값들이 falsy에 해당된다. 

```
- 0
- NaN
- "" (the empty string)
- 0n (the bigint version of zero)
- null
- undefined
```

**3) in operator**

in 연산자는 객체 내부의 속성을 검사하는 연산자다. 대상이 객체 형태이기만 하면 된다. 즉, 객체 type에도 적용된다. 

```
// 사용법 => if (property in object)

type Fish = { swim: () => void };
type Bird = { fly: () => void };
 
function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    return animal.swim();
  }
 
  return animal.fly();
}
```

**4) instanceof operator**

instanceof 연산자는 인스턴스의 프로토타입 체인 과정에서 생성자의 프로토타입을 발견할 수 있는지 검사하는 연산자다. 쉽게 말해서 인스턴스가 어떤 'new 생성자'로 형성되었는지 검사하는 연산자다.  

```
x instanceof Foo  
// x의 프로토타입 체인이 Foo.prototype을 포함하는지 검사한다.
// x가 'x = new Foo()' 형태로 생성되었다면 참을 반환한다.
```

