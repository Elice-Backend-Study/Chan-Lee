## **1\. 함수 타입 표기법**

타입스크립트에서 함수의 타입을 지정해줘야 하는 부분은 크게 두 파트로 나뉜다.

> **1\. 매개변수의 타입**  
> **2\. 반환값의 타입**

위 규칙에 따라 함수 타입 표기법을 정리하면 아래와 같다. 자바스크립트 코드와 비교할 수 있도록 같이 적었다. 참고로 공식문서에 따르면 **반환 타입은 일반적으로 타입스크립트가 추론 가능하므로 적지 않아도 되는 경우가 많다**고 한다. 

**기명 함수 타입 표기법**

```
// js
function add(x, y) {
  return x + y;
}

// ts
function add(x: number, y: number): number {
  return x + y;
}
```

**익명 함수 타입 표기법**

```
// js
let add = function(x, y) { return x + y; }


// ts - 아래 모두 가능
let add1 = function(x: number, y: number): number { return x + y; }

let add2: (x: number, y: number) => number = 
  function(x, y) { return x + y; };
  
let add3: (x: number, y: number) => number =
  function(x: number, y: number): number { return x + y; };
```

→ add1과 add2를 합친게 add3다. 모두 가능한 방법이다. 

**화살표 함수 타입 표기법**

```
// js
let add = (x, y) => {
  return x + y;
}


// ts - 아래 모두 가능
let add1 = (x: number, y: number): number => {
  return x + y;
}

let add2: (x: number, y: number) => number = (x, y) => {
  return x + y;
}

let add3: (x: number, y: number) => number = (x, y) => x + y;
```

→ 익명함수 타입 표기법과 큰 차이 없다. 

**콜백 함수를 받을때**

```
// js
function greeter(callback) {
  callback("Hello World!");
}

greeter((s) => { console.log(s) });


// ts
function greeter(func: (a: string) => void) {
  func("Hello World!");
}

greeter((s: string): void => { console.log(s); });
```

## **2\. 선택적 매개변수와 기본 매개변수**

**1) 선택적 매개변수, 나머지 매개변수**

선택적 매개변수는 자바스크립트에는 없는 기능이다. 정확하게 말하면 **자바스크립트에는 필요 없는 기능**이다. 왜냐하면 자바스크립트는 함수의 매개변수 개수와 받는 인자 개수가 일치하지 않아도 되기 때문이다. 예를 들어 지금 개발자 도구로 가서 아래 코드를 복사해서 실행시켜보면 오류 없이 실행될 것이다. 

```
function func(a, b, c) {
    console.log(a, b, c)
}

func(1, 2, 3, 4, 5); // 1 2 3
func(1, 2); // 1 2 undefined
```

그런데 타입스크립트는 다르다. **타입스크립트는 함수의 매개변수 개수와 받는 인자 개수가 반드시 일치**해야 한다. 위와 동일한 코드를 타입스크립트에서 실행시키면 컴파일 오류가 난다. 

```
type n = number;
function func(a: n, b: n, c: n): void {
  console.log(a, b, c)
}

func(1, 2, 3, 4, 5); // Expected 3 arguments, but got 5
func(1, 2); // Expected 3 arguments, but got 2.
```

함수가 인자를 받을때 발생 가능한 상황은 세 가지 케이스로 분류된다. 

```
// 함수 매개변수 = param
// 함수가 받는 인자 = arg

type n = number;
function func(a: n, b: n, c: n): void {
  console.log(a, b, c)
}

// 1. param > arg
func(1, 2, 3, 4, 5); // Expected 3 arguments, but got 5

// 2. param == arg
func(1, 2, 3)

// 3. param < arg
func(1, 2); // Expected 3 arguments, but got 2.
```

두번째 케이스는 정상적인 케이스고, 첫번째와 세번째 케이스는 오류가 난다. 다행히도 각 케이스별 해결책이 있다. 

**첫번째 케이스는 나머지 매개변수로 해결**할 수 있다. 자바스크립트에서도 쓰이는 개념이기 때문에 설명은 생략하겠다.

```
type n = number;
function func(a: n, b: n, ...c: Array<n>): void {
  console.log(a, b, ...c) // 이건 spread 연산자
}

func(1, 2, 3, 4, 5); // 1 2 3 4 5
```

**세번째 케이스는 선택적 매개변수로 해결**할 수 있다. 선택적 매개변수는 **매개변수 뒤에 '?'를 붙여주면 된다**. 이는 해당 매개변수를 인자로 받아도 되고, 안받아도 된다는 뜻이다. 주의해야할 점은 선택적 매개변수는 **항상 마지막에 위치**해야 한다. 또한 선택적 매개변수를 받지 않으면 undefined가 된다.

```
type n = number;
function func(a: n, b: n, c?: n): void {
  console.log(a, b) // 1 2
  console.log(c); // undefined
}

func(1, 2);
```

**2) 기본 매개변수** 

기본 매개변수는 자바스크립트와 똑같다. 인자를 덜 받았을때 자동으로 채워주는 기능이다. 아쉽게도 선택적 매개변수와 같이 쓸 수는 없다.

```
type n = number;
function func(a: n, b: n, c: n = 6): void {
  console.log(a, b, c)
}

func(1, 2); // 1 2 6
```

## **3\. 함수 오버로딩**

함수 오버로딩이란 **함수 이름은 같지만 매개변수 또는 반환값의 타입이 다른 함수**를 말한다. 자바스크립트는 함수 매개변수와 리턴타입에 굉장히 관대하기 때문에 함수 오버로딩이라는 개념이 적용되지 않는다. 왜냐하면 그냥 함수 하나만 만들면 어떤 타입도 인자로 받을 수 있기 때문이다. 반환 타입 또한 자유롭다. 그런데 타입스크립트는 함수의 매개변수와 바환값에 타입을 지정해줘야 하기 때문에 오버로딩을 적용할 수 있다. 

타입스크립트의 함수 오버로딩은 특이하게 **선언부**와 **구현부**로 나뉘는데 **선언부에서는 가능한 매개변수 타입만 지정**해주고, **함수 구현은 구현부에서만** 이루어진다. 그리고 구현부는 반드시 선언부의 마지막 부분에서 작성되어야 한다. 또한 **구현부는 모든 선언부를 표현할 수 있어야 한다**. 잘 와닿지 않으니 몇 가지 예시를 보자. 

**예시1**

```
// 선언부 1, 2
function makeDate(timestamp: number): Date;
function makeDate(m: number, d: number, y: number): Date;

function makeDate(mOrTimestamp: number, d?: number, y?: number): Date { // 구현부
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  } else {
    return new Date(mOrTimestamp);
  }
}

const d1 = makeDate(12345678);
const d2 = makeDate(5, 5, 5);
const d3 = makeDate(1, 3); // Error - 선언부에 의하면 매개변수는 1개 or 3개
```

구현부가 선언부를 모두 표현할 수 있는지만 살펴보면 된다. 우선 선언부와 구현부 모두 반환 타입은 일치하므로 매개변수만 확인하면 된다. 첫 번째 선언부는 구현부에서 else문일 경우 표현 가능하다. 두 번째 선언부는 구현부에서 if문일 경우 표현 가능하다. 그런데 **선언부를 보면 매개변수가 한개 또는 세개인 경우만 있으므로 makeDate의 인자로 값을 두 개만 받으면 에러가 발생**한다. 마지막 줄의 에러를 참고하자.  

**예시2**

```
// 선언부 1, 2
function len(s: string): number;
function len(arr: any[]): number;

function len(x: any): number { // 구현부
  return x.length;
}

len(""); 
len([0]); 
len(1); // Error - 선언부의 타입과 일치하지 않음
```

역시 구현부가 선언부를 모두 표현할 수 있는지만 살펴보면 된다. 반환 타입은 일치하고, 구현부의 매개변수 타입을 any로 둬서 선언부를 모두 표현할 수 있다. 여기서 주의할 점이 있다. **오버로딩된 함수를 사용할때 매개변수 타입 규칙은 선언부를 따른다. 선언부의 매개변수 타입에 number는 없으므로 마지막 줄에서 에러가 발생**한다.

사실 위의 오버로드 함수는 아래와 같이 표현할 수 있다. 

```
function len(x: any[] | string) {
  return x.length;
}
```

공식문서에 따르면 항상 **오버로드 보다는 유니온 타입을 쓰라고 한다**. 결론이 약간 허무하네...ㅎ

아래는 오버로드의 잘못된 예시다.

**예시1**

```
// 선언부
function fn(x: string): void; 

function fn() { // 구현부
  // ...
}
// Expected to be able to call with zero arguments
fn();
```

위 코드는 구현부가 선언부의 내용을 담고 있지 않아 에러가 발생한다. **선언부에는 한 개의 매개변수가 존재하지만 구현부에는 이를 표현할 수 없다**. 그런데 선언부가 한 개만 있으면 오버로딩을 쓸 이유가 전혀 없으므로 **오버로딩을 쓸때는 일반적으로 선언부가 최소한 두 개 이상** 있어야 한다. 

**예시2**

```
// 선언부 1, 2
function fn(x: boolean): void;
function fn(x: string): void;

function fn(x: boolean) {} // 구현부
```

위 코드는 두 번째 선언부와 구현부가 양립 가능하지 않으므로 에러가 발생한다. **구현부에서** **매개변수 타입이 string인 경우를 표현할 수 없다**. 

**예시3**

```
// 선언부 1, 2
function fn(x: string): string;
function fn(x: number): boolean;

function fn(x: string | number) { // 구현부
  return "oops";
}
```

위 코드 역시 두 번째 선언부와 구현부가 양립 가능하지 않다. 두 번째 선언부의 반환 타입은 boolean이지만 구현부의 반환 타입은 타입 추론에 의해 string이 되므로 구현부는 선언부를 표현할 수 없다. 즉, **선언부와 구현부의 반환 타입이 일치하지 않는다**.
