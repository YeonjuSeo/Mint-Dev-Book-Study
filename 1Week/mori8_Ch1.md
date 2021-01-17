# Chapter 1: 타입

> 2021.1.16(토)
>
> 책: You don't know JS

### 자바스크립트에도 타입이 있다

- '타입': 자바스크립트 엔진, 개발자 모두에게 어떤 값을 다른 값과 분별할 수 있는, 고유한 내부 특성의 집합
- 42(number) !== "42"(string)
- 타입에 따라 사용할 수 있는 함수와 연산, 가공 방법이 다름



### 자바스크립트의 내장 타입

- 자바스크립트의 7가지 내장 타입
  - `null`
  - `undefined`
  - `boolean`
  - `number`
  - `string`
  - `object`
  - `symbol(ES6부터 추가됨)`

object를 제외한 6가지 타입을 `원시 타입`이라고 한다.

값의 타입은 `typeof` 연산자를 통해 알 수 있지만, 그 반환값이 항상 7가지 내장 타입 중 하나인 것은 아니다. 

- `typeof` 반환값에 대한 예외

  - `null`

    - null을 제외한 모든 내장 타입은 자신의 명칭과 동일한 문자열을 반환한다.
    - 신기하게도 null 타입에 대한 반환값은 `object` 이다.
    - 해당 값이 null인지 확인하기 위해서는 아래와 같이 조건이 하나 더 필요하다.

    ```js
    var a = null;
    (typeof a) // object
    (!a && typeof a === "object"); // true
    ```

  - `function`

    - 내장 타입이 아닌 `object` 의 하위 타입이다.
    - 함수는 '호출 가능한 **객체**'이기 때문이다.

    ```js
    typeof function a() {/* ... */} === "function"; // true
    ```

    





### 변수에는 타입이 없다

변수에 `typeof` 연산자를 대어보는 것 === "이 변수에 들어있는 값의 타입은 무엇이니?" !== "이 변수의 타입은 무엇이니?"



### not defined !== 'undefined'

```js
var a;
a; // undefined
b; // ReferenceError: b is not defined

typeof a; // "undefined"
typeof b; // "undefined" -> 에러도 안 뱉는다??
```



### 전역변수의 존재를 체크하는 3가지 방법

1. 바로 위 코드를 통해서 알아본 `typeof undefined`의 애매모호함(?) 이용하기(추천!)

   ```js
   if (typeof VALUE !== "undefined") { ... } // 선언되지 않은 경우, 값이 없는 경우 둘 다 걸러냄
   ```

   굳이 전역변수가 아니어도 변수가 유효한지 확인하고 싶을 때 사용하면 된다.

   

2. 전역 변수가 전역 객체(브라우저의 경우 window)의 프로퍼티라는 점 이용하기(비추천)

   ```js
   if (window.VALUE) { ... }
   ```

   서버에서 실행되는 node.js같은 경우는 전역 객체가 window가 아니므로 사용에 주의해야 한다.

   

3. 의존성 주입

   ```js
   function foo(bar) {
     var helper = bar ||
         function () { /* bar가 없을 때 실행될 함수 */ };
     
     var val = helper();
     // ...
   }
   ```

   위 코드처럼 명시적으로 의존 관계를 전달할 수도 있다.



