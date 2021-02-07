# [2021 겨울 민트하임 스터디] You don't know JS: Chapter 4: 강제변환(1)

> 2021.02.07(일)
>
> 챕터 4는 양이 많아서 값 변환/명시적 강제변환과 암시적 강제변환/동등성 비교의 3개로 나누어 정리하겠다.

## 명시적 강제변환과 암시적 강제변환

```js
let a = 7;
let b = "오늘은 " + a + "일"; // 암시적 강제변환
let c = String(a); // 명시적 강제변환
```

`명시적 강제변환`은 코드에서 의도적으로 타입 변환이 이뤄진다는 사실이 명확하게 드러나는 반면, `암시적 강제변환`은 다른 작업으로 인한 부수 효과로 인해 발생하는 타입 변환이다.

#### b의 경우: 암시적 강제변환

암시적 강제변환이 발생한다. 문자열과의 `+` 연산은 두 <u>문자열</u>을 합치는 문자열 접합을 의미하므로, 문자열 접합의 부수 효과로서 숫자 7을 문자열 "7"로 암시적 강제변환했다.

#### c의 경우: 명시적 강제변환

명시적 강제변환이 발생한다. `String()`은 값을 인자로 받아 명백히 문자열 타입으로 강제변환한다.



## 변환은 어떻게 이루어지는가

변환은 '추상 연산' 로직에 의해 이루어진다. 

### ToString

문자열이 아닌 값을 문자열로 변환하는 작업은 `ToString` 추상 연산 로직이 담당한다. 내장 원시 값들은 본연의 문자열화 방법이 정해져 있으며(예: null -> "null", undefined -> "undefined", true -> "true"), 숫자는 바로 문자열로 변환되지만 너무 작거나 큰 값은 지수 형태로 바뀐다.

```js
let a = 1.07 * 1000 * 1000 * 1000 * 1000 * 1000 * 1000 * 1000;
a.toString(); // "1.07e21";
```



일반 객체는 기본적으로 Object.prototype.toString() `toString()` 메서드는 내부 [[Class]]를 반환한다. 자신만의 커스터마이징된 `toString()` 메서드를 가진 객체는 여기서 설명하고 있는 메서드가 아닌 자신의 `toString()` 메서드를 호출한다(오버라이딩?).

배열은 기본적으로 재정의된 `toString()`이 있으며, 호출 시 모든 원소 값이 각각 문자열로 바뀌어 콤마(,)로 분리된 형태로 이어진다.

```js
let a = [1, 2, 3];
a.toString(); // "1, 2, 3"
```



#### JSON 문자열화

`JSON.stringify()` JavaScript 값이나 객체를 JSON 문자열로 변환한다. JSON 표현형으로 확실히 나타낼 수 있는 값(undefined, 함수, symbol, 환형 참조 객체 제외 값)은 모두 `JSON.stringify()` 로 문자열화할 수 있다.

`JSON.stringify()`의 인자로 undefined, 함수, symbol 값이 들어오면 자동으로 누락시키며, 이런 값들이 배열에 포함되어 있으면 배열 인덱스 정보가 뒤바뀌지 않도록 null로 바꾼다. 객체 프로퍼티에 있으면 지워버린다. 환영 참조 객체가 들어오면 에러를 뱉으니 넣지 말자.

```js
JSON.stringify(undefined); // undefined
JSON.stringify(() => {});	 // undefined

JSON.stringify(
	[1, undefined, () => {}, 4]
); // "[1, null, null, 4]"

JSON.stringify(
	{ a: 2, b: function(){}}
); // "{"a": 2}"
```

`JSON.stringify()` 함수에 두번째 선택 인자로 배열 또는 함수 형태의 대체자를 지정하면 프로퍼티 필터링이 가능하다. 대체자가 배열인 경우 배열의 전체 원소는 문자열이어야 하며 이 배열에 포함되지 않은 프로퍼티는 직렬화 과정에서 빠진다,.함수인 경우는 건너뛰고자 하는 key에 대해 undefined를 반환하도록 하면 된다.

```js
let a = {
  b: 13,
  c: "13",
  d: [1, 2, 3]
};

JSON.stringify(a, ["b", "c"]); // "{"b": 13, "c": "13"}"
JSON.stringify(a, (k, v) => {
  if (k !== "c") return v;
}); // "{"b": 13, "d": [1, 2, 3]}"
```





#### toJSON()

`toJSON()` 메서드는 해당 객체의 JSON 안전 버전을 반환하는 기능을 수행한다. `JSON.stringify()`의 인자로 들어오는 객체에 `toJSON()` 메서드가 정의되어 있다면 문자열로 변환하기 전 `toJSON()` 을 호출하여 직렬화한 값을 가져온다.

`toJSON()` 은 문자열하기 적당한 JSON 안전 값으로 바꾸는 메소드며, JSON 문자열로 바꾸는 역할은 하지 않는다는 걸 기억해 두자. 문자열로 바꾸는 역할은 `JSON.stringify()` 가 담당한다.

```js
let a = {
  b: 42,
  c: "hello",
  d: function(){}
};

a.toJSON = function() {
  return { b: this.b }; // 직렬화에 프로퍼티 'b'만 포함시킨다.
}

JSON.stringify(a); // "{"b": 42}"
```





### ToNumber

숫자가 아닌 값을 수식 연산이 가능한 숫자로 변환하는 로직은 ToNumber 추상 연산에 잘 정의되어 있다. 원시 값에 대한 ToNumber 연산은 아래 표를 참고하자.

| value        | true | false | undefined | null | String           |
| ------------ | ---- | ----- | --------- | ---- | ---------------- |
| **ToNumber** | 1    | 0     | NaN       | 0    | 변환 실패 시 NaN |

숫자로의 강제변환 방법은 다음 글에서 자세하게 설명하겠다.



### ToBoolean

대부분의 언어에서는 1과 0이 각각 true와 false에 대응되지만, 자바스크립트에서는 숫자는 숫자고 boolean은 boolean이다. `1 !== true`라는 거다.

#### Falsy

false도 아니고, falsy는 뭘까? falsy는 boolean 값으로 강제변환 시 false가 되는 <u>몇 안되는</u> 값들을 가리킨다(몇 안되니까 기억해두자). 아래 값들이 그 몇 안된다는 'falsy' 값이다. 이게 전부다.

- undefined
- null
- false
- +0, -0, NaN
- ""

이 목록에 없다면 'truthy'한 값이다. 위 값들을 제외한 모든 값들은 'truthy'하며, Boolean으로 변환했을 때 true가 된다.



#### Falsy 객체

falsy 객체는 falsy 값을 감싼 객체가 아니라, 겉보기에는 평범한 객체처럼 truthy할 것 같지만 boolean으로 변환 시 false가 되는 객체들이다. 가장 대표적인 falsy 객체에는 `document.all` 과 유사 배열이다. 

`document.all`이 falsy한 이유는 비표준인 `document.all`이 표준을 준수하는 현대 IE 환경에서 잘 돌아가게 두지 않게 하기 위함이다.

