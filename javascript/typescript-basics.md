# typescript basics

typescript: superset of javascript

생성 후 javascript로 컴파일 해야됨.

```
npm install typescript
tsx myfile.tx
```

## Type Interface

typescript는 타입이 선언되지 않을 경우 타입을 추론함.

그래서 아래와 같이 number로 선언된 변수에 다른 타입의 값을 넣으면 컴파일 에러가 발생한다.

```
let myId = 5;
myId = 'test string';
```

## Type Annotation

typescript가 위에서 보았듯이 타입을 추론해주기는 하지만, 최대한 이를 명시해주는게 좋다.

변수의 타입 명시는 직관적이다. python typing이랑 거의 흡사한듯

```
let studentId: number = 1;
let studnetName: string = 'myname';
let isStudent: boolean = true;
```

함수의 파라미터와 리턴에도 타입 명시를 해준다.

주의할 점은 아래와 같이 특정 타입을 명시했는데 return 값이 없으면 에러가 난다는 점이다.

리턴값이 없을 경우 `void` 타입을 명시해준다.

```
function getStudentName(studentId: number): string {}
```


## Type interface

기본 타입 외에 object등을 리턴해야 할 때에는 이를 직접 쓸 수도 있지만 interface를 사용해서 써 주면 좋다.

```
// 직접 object를 타입으로 명시
function getStudentDetails(studentId: number) {
  studentId: number,
  studentName: string,
  isStudent: boolean
} {
  return {
    studentId: studentId;
    studentName: 'name';
    isStudent: true;
  }
}
```

위 코드를 interface를 사용해서 구현하면 아래와 같다.

```
interface Student {
  studentId: number,
  studentName: string,
  isStudent: boolean
}

function getStudentDetails(studentId: number) Student {
  return {
    studentId: studentId;
    studentName: 'name';
    isStudent: true;
  }
}
```

> interface라는 것을 지칭하기 위해 `iStudent`라고 쓰기도 한다는데 typescript 문서에서는 그냥 일반적인 형태로 쓰라고 convention 가이드가 있다.

### Optional

`?`를 써서 optional을 추가할 수 있다. 위 예시에서 `isStudent`를 optional로 받는 코드이다.

```
interface Student {
  studentId: number,
  studentName: string,
  isStudent?: boolean  // optional
}
...
```

### Method

method도 인터페이스에서 추가해줄 수 있다.

```
interface Student {
  readonly studentId: number,  // readonly
  studentName: string,
  isStudent?: boolean,
  addComment?: (comment:string) => string
  // addcomment (comment: string): string // 이렇게 써줘도 된다.
}
```


### Readonly

객체 생성시 할당된 프로퍼티를 바꿀수 없다. 앞에 readonly를 추가해주기만 하면 됨.

```
interface Student {
  readonly studentId: number,  // readonly
  studentName: string,
  isStudent?: boolean,
  addComment?: (comment:string) => string
}
```

## Enum & literal type

특정 값만 해당 타입에서 사용하고 싶으면 enum이나 literal type을 사용하면 된다.

Student object에 `gender` 프로퍼티를 추가해보자. `male`, `female`로 제한해보자.

### Enum



```
enum GenderType {
  Male,
  Femail
}

interface Student {
  readonly studentId: number,  // readonly
  studentName: string,
  isStudent?: boolean,
  gender: GenderType,
  addComment?: (comment:string) => string
}
```


기본적으로 enum은 값을 0부터 순차적으로 붙여준다. 따라서 위에서는 `Male = 0`, `Female = 1`로 값이 들어간다.

string으로 넣어주고 싶으면 직접 값을 지정해주면 된다.

```
enum GenderType {
  Male = 'male',
  Femail = 'female'
}

interface Student {
  readonly studentId: number,  // readonly
  studentName: string,
  isStudent?: boolean,
  gender: GenderType,
  addComment?: (comment:string) => string
}
```

### Literal type

위와 같이 enum 객체를 생성하지 않고 바로 나열해 줄 수도 있다.

```
interface Student {
  readonly studentId: number,  // readonly
  studentName: string,
  isStudent?: boolean,
  gender: 'male' | 'female',
  addComment?: (comment:string) => string
}
```

하지만 이 경우 코드에서 직접 값을 자주 사용해야 하므로 enum을 사용하는 편이 좋을듯하다.



## Any, Union, type alias

특정 값이 들어올지 모르는 상황이라면 모든 타입을 허용하는 `any` 타입을 써도 된다.

하지만 그보다는 명시적으로 들어올 수 있는 모든 타입을 union type으로 지정해주는 것이 좋다.

```
let price: number | string = 5;
price = 'free';
```

### type alias

이런 union type 반복적으로 발생하면 type alias를 생성해주는게 좋다.

```
type StrOrNum = number | string;
let price: StrOrNum = 5;
price = 'free';
```

이런식으로 여러 타입이 올 수 있는 경우에는 type guard를 추가해주는 것이 좋다.

아래는 StrOrNum type으로 받은 값을 itemPrice에 할당해주는 코드이다.

```
type StrOrNum = number | string;
let itemPrice: number;

const setItemPrice = (price: StrOrNum):void => {
  itemPrice = price;
}
```

별 문제가 없어 보이지만, `itemPrice` 변수는 number type이므로 string일 수 있는 `StrOrNum` 때문에 에러가 발생한다.

이를 해결해 주기 위해 각 타입별 처리를 해줘야한다.

```
type StrOrNum = number | string;
let itemPrice: number;

const setItemPrice = (price: StrOrNum):void => {
if (typeof price === 'string') {
  itemPrice = 0;
} else {
  itemPrice = price;
}
}
```

