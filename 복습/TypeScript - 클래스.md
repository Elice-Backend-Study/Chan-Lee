### 1. 클래스 구성요소(Members)

1) 필드(field)

- 클래스 내부 변수. 보통 필드 보다는 '멤버 변수'로 많이 부른다.

- 클래스 내부에서 타입과 함께 선언.

- 필드는 클래스 내에서 생성자에 의해 반드시 초기화되어야 한다.

```
class Point {
    x number;  Error - 생성자 초기화 안해줌
    y! number;  ! 옵션은 undefined로 자동 초기화
    readonly z number  인스턴스 생성 이후 변경 불가
}
```

- ! 옵션  필드 이름 뒤에 !를 붙여주면 undefined로 자동 초기화된다. 따라서 생성자에서 초기화 안해줘도 에러 안난다.

- readonly 옵션  필드 이름 앞에 readonly 옵션을 넣어주면 해당 필드는 인스턴스 생성 이후 변경 불가. 즉, 생성자 안에서만 초기화 가능.

2) 생성자(constructor)

- 필드 초기화 함수로, 함수명은 constructor로 고정된다.

- new 연산자와 함께 호출되면서 새로운 객체를 생성한다. 이때 생성된 객체를 '인스턴스'라고 한다. 

- 클래스 내애서 생성자는 오직 한개만 존재할 수 있다.  

- this 는 클래스 자신을 말한다.

```
class Point {
  x number;
  y number;
 
  constructor(x = 0, y = 0) {
    this.x = x;
    this.y = y;
  }
}
```

3) 메소드(method)

- 클래스 내 생성자 이외의 함수들.

- 함수 선언시 'function'이 생략된 것에 주의하자. 

```
class Pdoint {
  x number;
  y number;
 
   생성자
  constructor(x = 0, y = 0) {
    this.x = x;
    this.y = y;
  }

   메소드
  distance() number {
    return Math.pow(this.x - this.y, 2);
  }
}
```

### 2. 상속(extends)

```
class Person {
  name string;
  idNum string;

  constructor(_name string, _idNum string){
    this.name = _name;
    this.idNum = _idNum;
  }

  print() {
    console.log(`Person ${this.name}, ${this.idNum}`);
  }
}

class Student extends Person {
  major string;

  constructor(_name string, _idNum string, _major string) {
    super(_name, _idNum);  super로 부모 멤버 사용 가능
    this.major = _major;  부모 생성자 호출 뒤에 가능
  }

  print() {
    console.log(`Student ${this.name}, ${this.idNum}`);
  }

  getMajor() {
    console.log(`major ${this.major}`);
  }
}


 부모 타입은 자식 타입 대체 가능
const student Person = new Student(charles, 1, computer science);

 Error 자식 타입은 부모 타입 대체 불가
const person Student = new Person(charles, 1);
```

- Stduent 클래스가 Person 클래스를 상속받음.

- Student 클래스는 Person 클래스의 모든 멤버를 가진다. 단, Person 클래스의 private 멤버는 제외한다. 

- 자식 클래스에서 'super' 키워드를 통해 부모 클래스 멤버를 사용할 수 있다. 

- 자식 클래스의 생성자에서 반드시 super로 부모의 생성자를 먼저 호출해야 한다. 

- 부모 타입은 자식 타입을 대체할 수 있다. 역은 성립하지 않음.

더보기

마지막 문장에 대해 좀 더 생각해보자.

Q 부모 타입은 자식 타입을 대체할 수 있다. 왜  
A 자식 클래스는 부모의 모든 멤버를 상속받기 때문이다. 부모한테 있는건 자식한테도 있기 때문에 자식의 타입 구조는 부모의 타입 구조 또한 만족시킨다.  
  
Q 자식 타입은 부모 타입을 대체할 수 없다. 왜  
A 자식은 부모의 모든것을 가지지만, 부모는 자식의 모든 것을 가지지 않기 때문이다.

상속의 의미를 잘 생각해보면 당연한 사실이다. 

위 코드에서 흥미로운 점이 하나 있다. 우리는 student 변수 타입을 Person으로 지정해줬고, 인스턴스 생성은 Student 클래스로 했다. 여기서 문제. student 변수는 어떤 클래스의 인스턴스일까 정답은 Person 클래스다. 따라서 student 변수에 적용 가능한 메소드는 print 뿐이고, 멤버 변수로 major를 가지지 않는다. 아래와 같이 코드 변형이 이루어진 것으로 볼 수 있다. 

```
const student Person = new Student(charles, 1, computer science);

 코드 변형 

const student Person = new Person(charles, 1);
```

### 3. 접근 제어자

1) private

private로 선언된 멤버는 외부에서 어떤 경우라도 사용할 수 없다.

2) protected

protected로 선언된 멤버는 상속받은 경우 자식 클래스에서 private처럼 사용 가능하다.

3) public

public으로 선언된 멤버는 외부에서(상속 포함) 제한없이 사용 가능하다.

```
class Base {
  private x() { console.log(private) };
  protected y() { console.log(protected) };  
  public z() { console.log(public) ;}
}

class Derived extends Base {
  someMemberFunc() {
    super.x();  Error private 멤버이므로
    super.y();
    super.z();
  }
}

class Unrelated {
  base Base = new Base();

  someMemberFunc() {
    this.base.x();  Error private 멤버이므로
    this.base.y();  Error protected 멤버이므로
    this.base.z();
  }
}
```

## 4. Static 멤버

클래스 내부에서 멤버 앞에 static이 붙으면 해당 멤버는 클래스의 전역 멤버라는 뜻이다. Static 멤버는 인스턴스로 접근할 수 없고, 오직 클래스로만 접근할 수 있다. 

```
class MyClass {
  static x = 0;
  static printX() {
    console.log(MyClass.x);
  }
}
console.log(MyClass.x);
MyClass.printX();
```

static 멤버는 아래와 같이 접근제어자와 같이 쓰일 수 있다. 

```
class MyClass {
  private static x = 0;
}
console.log(MyClass.x);  Error private 접근 불가
```

static 멤버도 같이 상속된다.

```
class Base {
  static x = static;
}
class Derived extends Base {
  print() {
    console.log(Base.x);  static
    console.log(Derived.x);  static
  }
}
```

## 5. 추상 클래스(abstract)

- 추상 클래스는 인터페이스와 유사하다.

- 클래스 선언시 앞에 'abstract' 키워드를 넣어주면 된다. 

- 메소드 앞에 'abstract' 키워드를 넣어주면 해당 메소드는 본문을 작성하지 않는다.

- 'abstract' 키워드가 없는 메소드는 구현한다는 점이 인터페이스와 다르다.

- 추상 클래스는 인스턴스를 생성할 수 없다. 즉, 생성자가 존재하지 않는다. 단, 타입으로는 사용 가능하다. 

- 추상 클래스를 상속받은 자식 클래스는 반드시 추상 메소드를 구현해야 한다.  

```
type n = number;  타입 별칭 지정


 1. 추상 클래스
abstract class Shape {
  abstract area() n;
  abstract perimeter() n; 
}


 2. 직사각형 클래스
class Rectangle extends Shape {
  width n; 
  height n;

  constructor(width n, height n) {
    super();
    this.width = width;
    this.height = height;
  }

  area() { return this.width  this.height }
  perimeter() n { return 2this.width + 2this.height }
}


 3. 원 클래스
class Circle extends Shape {
  radius n;
  pi = Math.PI;

  constructor(radius n) {
    super();
    this.radius = radius;
  }

  area() { return this.pi  this.radius  this.radius }
  perimeter() n { return 2  this.pi  this.radius }
}


 타입은 Shape로 고정시키고, 생성자는 자유롭게!
const shapeRec Shape = new Rectangle(2, 3);
const shapeCir Shape = new Circle(4);
```

코드의 마지막 부분에 주목하자. 타입은 Shape(부모 클래스)로 고정시키고, 생성자는 자유롭게 사용하고 있다. 아까 '상속' 파트에서 말했듯이 인스턴스 생성시 타입을 부모 클래스로 지정해주면 구조는 부모 클래스를 따른다. 그런데 추상 클래스는 생성자를 가지고 있지 않기 때문에 자식 클래스의 생성자가 그대로 적용된다. 그리고 추상 메소드는 자식 클래스에서 구현되기 때문에 자식 클래스의 메소드가 바인딩된다. 이렇게 부모 클래스는 단순히 인터페이스만 제공하고, 실질적인 기능 구현은 자식 클래스에서 할때 추상 클래스가 사용된다. 

쉬운 이해를 위해 Shape가 추상 클래스일때와 추상 클래스가 아닐때를 비교해봤다.

```
const shapeRec Shape = new Rectangle(2, 3);

1. Shape가 추상 클래스가 아닌 경우
- Shape의 생성자가 적용된다
- shapeRec은 Rectangle의 메소드를 사용할 수 없다.
- shapeRec은 Shape의 메소드만 사용할 수 있다.


2. Shape가 추상 클래스인 경우
- Rectangle의 생성자가 적용된다. (Shape는 생성자가 없기 때문)
- shapeRec은 Rectangle 내 구현된 추상 메소드를 사용할 수 있다.
- shapeRec은 Shape의 일반 메소드도 사용할 수 있다. (추상 메소드 제외)
```

참조

[httpswww.typescriptlang.orgdocshandbook2classes.html#class-members](httpswww.typescriptlang.orgdocshandbook2classes.html#class-members)    

[httpstypescript-kr.github.iopagesclasses.html](httpstypescript-kr.github.iopagesclasses.html)