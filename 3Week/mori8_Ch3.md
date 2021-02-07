## 네이티브

자바스크립트의 내장 함수다. 정확히는 특정 환경(브라우저 등의 클라이언트 프로그램, 또는 Node.js 등의 런타임)에 종속되지 않은, ECMAScript 명세의 내장 객체다. 내장 타입 생성자라고 생각하면 될 것 같다.

```
var s = new String("Hello, world!");

typeof s; // "object" -> ?
s instanceof String; // true -> ?
```

엥? 생긴건 생성자와 비슷하지만 결과는 우리의 예상과 다르다. `new String("Hello, world!")`의 결과는 원시 값 "Hello, world!"를 감찬 객체 래퍼다.

typeof 연산자로 이 객체의 타입을 확인해 보면 자신의 감싼 원시 값의 타입이 아닌 Object의 하위 타입에 가깝다.

결론: `new String("Hello, world!")`는 "Hello, world!"를 감싸는 문자열 래퍼를 생성하며, 원시 값(string) "Hello, world!"는 아니라는 점이다.

## 내부 \[\[Class\]\]

typeof의 결과가 `object`인 값에는 `[[Class]]`라는 내부 프로퍼티(내부 분류법의 일부?)가 추가로 붙는다. 이 프로퍼티에 직접 접근할 수는 없고, `Object.prototype.toString()`라는 메서드를 통해 알 수 있다.

#### 1) typeof something === "object"인 경우

```
Object.prototype.toString.call( [1, 2, 3] ) // "[object Array]"
Object.prototype.toString.call( /regex-literal/i ) // "[object RegExp]"
```

배열의 경우 내부 \[\[Class\]\] 값이 Array, 정규표현식의 경우 RegExp임을 알 수 있다. 대부분의 경우 내부 \[\[Class\]\]는 해당 값과 관련된 내장 네이티브 생성자를 가리키지만 그렇지 않을 때도 있다. (아래)

#### 2) typeof something !== {원시값}인 경우

분명 typeof의 결과가 `object`인 값에 `[[Class]]`라는 내부 프로퍼티가 있다고 앞에서 말했지만, 원시 값에도 내부 \[\[Class\]\]가 있다.

##### null, undefined

```
Object.prototype.toString.call( null ) // "[object Null]"
Object.prototype.toString.call( undefined ) // "[object Undefined]"
```

Null, undefined의 경우 `Null()`, `Undefined()` 같은 네이티브 생성자는 없지만 내부 \[\[Class\]\] 값은 Null, Undefined이다.

##### string, number, boolean 등의 단순 원시 값

`박싱` 과정을 거친다.

```
Object.prototype.toString.call( "hello" ) // "[object String]"
Object.prototype.toString.call( 13 ) // "[object Number]"
Object.prototype.toString.call( true ) // "[object Boolean]"
```

단순 원시 값들은 해당 객체 래퍼로 자동으로 박싱된다.

## 박싱과 언박싱

자바스크립트는 원시 값에 대해 자동 박싱을 지원해준다. 원시 값(string, number, boolean, symbol 등)은 프로퍼티가 메서드가 없는데 `"hello".charAt(1);`이 정상적으로 실행되는 이유도 바로 자바스크립트가 원시 값을 알아서 해당 객체 래퍼로 박싱하기 때문이다.

#### 수동 박싱은 피하자

원시 값의 프로퍼티/메서드를 빈번하게 사용할 일이 생긴다면 자바스크립트 엔진이 암시적으로 객체 래퍼로 박싱해 줄 필요가 없도록 처음부터 값을 객체로 생성하는 것이 맞다고 생각할 수도 있다.

하지만, 브라우저..님은 이런 경우를 스스로 최적화하기 때문에 오히려 개발자가 직접 '선 최적화'를 하게 되면 프로그램이 더 느려질 수 있다(?!). 그러니 우리는 그냥 알기 쉽게 원시 값들을 사용하자.

#### 언박싱

객체 래퍼의 원시 값은 `valueOf()` 메서드로 추출할 수 있다.

```
let a = new String("hello");

a; // String {"hello"}
a.valueOf(); // "hello"

let b = a + " world!"; // "hello world!"
```

6번째 줄에서는 암시적 언박싱이 일어난다(강제변환).

프로토타입에 대한 내용은 아직 이해를 못하기도 했고 워낙 내용도 많아서 프로토타입을 주제로 따로 글을 작성하며 공부할 예정이다.
