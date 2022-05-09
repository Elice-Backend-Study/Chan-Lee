## **1\. 인터페이스**

\- 인터페이스는 **객체의 타입 체크**를 위해 사용되며 **변수, 함수, 클래스에 사용**할 수 있다

\- **객체(Object)만 대상**으로 하며 내부에 프로퍼티와 메소드를 가질 수 있다.

\- 인터페이스 내부에서는 단지 프로퍼티 또는 메소드의 타입만을 정의한다.

\- **객체의 타입 별칭**으로 생각하면 된다.

**1) 변수에 인터페이스 적용**

```
interface Student {
  major: string; // 선택적 프로퍼티 적용 => major?: string;
  id: number; // 읽기전용 프로퍼티 적용 => readonly id: number;
  sayHello(): void;
}

let stu1: Student;

stu1 = {
  major: 'computer science',
  id: 1,
  sayHello() {
    console.log('Hello');
  }
}
```

\- sut1 객체는 타입 정의에 따라 두 개의 프로퍼티(major, id)와 한 개의 메소드(sayHello)를 가져야 한다.

\- 프로퍼티에 '?' 또는 'readonly' 키워드를 적용시킬 수 있다.

\- **선택적 프로퍼티**(? 키워드) : 타입 체크시 해당 프로퍼티는 없어도 상관없다는 뜻이다.

\- **읽기전용 프로퍼티**(readonly 키워드): 적용시 초기 값 할당 이후 값 수정이 불가하다.

**< Indexable Type >**

인터페이스의 프로퍼티로 모든 문자 또는 숫자를 대체할 수 있는 방법. 사전 패턴 구현시 적용 가능하다. 인덱스 자료형은 string, number만 가능하다.

```
// 1) 문자 인덱스
interface Dictionary {
  [index: string]: string;
}

let dict: Dictionary;

dict = {
  "Dog": "wolf",
  "Cat": "meow"
}



// 2) 숫자 인덱스
interface Ary {
  [index: number]: string;
}

let ary: Ary;

ary = {
  1: 'one',
  2: 'two'
}
```

**2) 함수에 인터페이스 적용**

```
interface Print {
  (input: string | number): void;
}

const print: Print = (input) => {
  console.log(input);
}
```

**3) 클래스에 인터페이스 적용**

```
interface PersonInt {
  name: string;
  age: number;
  setname(_name: string): void;
}

class Person implements PersonInt {
  public name;
  public age;

  constructor(_name: string , _age: number) {
    this.name = _name;
    this.age = _age;
  }

  setname(_name: string) {
    this.name = _name;
  }

  // interface 외 구성요소
  print() {
    console.log(`${this.name} , ${this.age}`)
  }
}
```

\- 인터페이스를 클래스에 적용시킬 때는 **implements 키워드**를 사용해야 한다.

\- 인터페이스를 클래스의 구성요소(implements)로 가진다는 의미.

\- **클래스는 interface의 구성요소를 다 갖춰야 하지만, interface 외 요소도 가질 수 있다**.

< 생성자 함수 인터페이스 >

```
interface newPerson {
  new (name: string, age: number): Person // PersonInt면?
}

function createPerson(construct: newPerson, name: string, age: number) {
    return new construct(name, age);
}

const person1 = createPerson(Person, 'Charles', 26);
person1.print();
```

\- 생성자 함수 인터페이스에는 **new 키워드**가 사용된다.

\- 생성자 함수 인터페이스의 반환 타입이 PersonInt면 오류가 발생한다. PersonInt에는 print 메소드를 적어주지 않았기 때문이다.

\- PersonInt에 print 메소드를 추가해주고, 생성자 인터페이스의 반환 타입을 PersonInt로 바꿔주면 정상 작동한다.

**4) 인터페이스 상속**

```
interface A {
  a: number;
}

interface B extends A {
  // a: number;
  b: number;
}

// 2개 이상 상속 가능
interface C extends A, B {
  // a: number;
  // b: number;
  c: number;
}
```

\- 인터페이스간 상속은 **extends 키워드**를 사용한다.

\- 2개 이상 상속 가능하다.

\- 상속받은 속성은 모두 인터페이스 구조에 추가된다.

\- 클래스도 상속 받을 수 있다.

< 클래스 상속 >

```
class AB {
  constructor(public a: number, public b: number) {}

  print() {}
}

interface C extends AB {
  // a: number
  // b: number
  // print() {}
  c: number;
}
```

\- **클래스의 모든 멤버(public, protected, private)가 상속되지만 함수는 추상 메소드로 상속된다.**

\- 구현부를 제외한 모든 구조가 상속된다.

## **2\. 제네릭**

\- 코드가 수행될 때 타입을 명시하여 다양한 타입을 사용할 수 있도록 하는 기법이다.

\- <> 안에 식별자를 써서 아직 정해지지 않은 타입을 표시할 수 있다.

\- <> 안에 적은 **식별자는** **블록 내 어디서든 사용 가능**하다.

\- 이후 사용할 때는 <> 안에 정확한 타입을 적어줘야 한다.

**1) 제네릭 함수**

```
// 1. 함수 선언식
function output1<Type>(input: Type): Type {
  return input;
}


// 2. 객체 리터럴 타입의 함수 호출 시그니처 - 인터페이스로 뺄 수 있다
const output3: { <Type>(input: Type): Type } = (input) => {
  return input;
}


// 3. 함수 표현식
const output2: <Type>(input: Type) => Type = (input) => {
  return input;
}


console.log(output1<number>(1)); // 1
console.log(output2<string>('1')); // "1"
console.log(output3<boolean>(true)); // true
```

**2) 제네릭 인터페이스**

```
// 1. 제네릭 인터페이스
interface GenericOutput1<Type> {
  (input: Type): Type;
}


// 2. 인터페이스 내부에서 제네릭 사용
interface GenericOutput2 {
  <Type>(input: Type): Type;
}


function output<Type>(input: Type): Type {
  return input;
}


const myOutput1: GenericOutput1<number> = output;
const myOutput2: GenericOutput2 = output;


console.log(myOutput1(1)); // 1
console.log(myOutput2<string>('1')); // "1"
```

\- **GenericOutput1**은 제네릭 인터페이스이므로 **인터페이스 적용 시에 타입을 명시**해줬다.

\- **GenericOutput2**는 구성요소가 제네릭이므로 **함수 호출 시에 타입을 명시**해줬다. 

**3) 제네릭 클래스** 

```
class Queue<Type> {
  data: Array<Type> = [];
  push(item: Type) {
    this.data.push(item);
  }
  pop(): Type | undefined {
    return this.data.shift();
  }
}

const numberQueue = new Queue<number>(); // 타입 명시
const stringQueue = new Queue<string>(); // 타입 명시
```

\- 제네릭 인터페이스와 형태가 유사하다. Type 식별자는 블록 내 어디서든 사용 가능하다. 

\- static 멤버는 클래스의 타입 매개변수를 쓸 수 없다. 

\- 생성자 함수를 호출할때 타입 명시를 해준다. 

**< 생성자 함수에 제네릭 적용 방법 >**

```
function create<Type>(c: { new (): Type }): Type {
  return new c();
}
```

**4) 제네릭 제약조건** 

```
function returnLength<T>(input: T): number {
  return input.length;
}
```

위 코드는 **에러**가 표시된다. 왜냐하면 **타입 T가 length 속성을 가지고 있는지 확신할 수 없기 때문**이다. 오류를 없애주기 위해서는 '타입 T에는 length 속성이 있어~'를 컴파일러에게 알려줘야 한다. 이렇게 타입 T에 length 속성이 있다는 것을 보장해주기 위해 제약조건을 걸 수 있다. 구문은 아래와 같다. 

```
// 제네릭 제약조건 구문

<T extends Type>
```

여기서 Type은 일반적으로 인터페이스를 적는다. extends를 상속으로 이해해도 전혀 문제가 없다. 상속으로 이해할 경우 Type의 모든 속성이 T로 이전되며, 제네릭 함수/인터페이스/클래스는 T의 모든 구조를 가지고 있어야 한다.

아래는 아까 에러가 표시됐던 코드에 제약조건을 추가한 버전이다. 객체 리터럴 타입을 먼저 적어주고 이를 인터페이스로 빼봤다. 

```
// 1. 객체 리터럴 타입
function returnLength<T extends { length: number }>(input: T): number {
  return input.length;
}



// 2. 인터페이스로 빼주기
interface Constraint {
  length: number;
}

function returnLength<T extends Constraint>(input: T): number {
  return input.length;
}

returnLength<string>('1'); // 가능
returnLength<number>(1);   // 불가능
```

\- **returnLength<string>** : string은 Constraint도 만족하는가? String은 length 속성이 있으므로 가능

\- **returnLength<number>** : number는 Constraint도 만족하는가? Number는 length 속성이 없으므로 불가능