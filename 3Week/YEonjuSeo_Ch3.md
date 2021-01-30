# Chapter 3 this

## 목표
- 상황별로 this가 어떻게 달라지는지와 그 이유 알기
- 예상과 다른 대상을 바라보고 있을 경우 그 원인을 효과적으로 추적하는 방법 알기

## 01 상황에 따라 달라지는 this
this는 함수를 호출할 때 결정된다.
### 전역 공간에서의 this
- 전역 공간에서 this는 전역 객체를 가리킨다. 브라우저 환경에서 전역객체는 window이고 Node.js 환경에서는 global이다.
```Javascript
var a=1;
console.log(a); //1
console.log(window.a); //1
console.log(this.a); //1
```
전역변수와 전역객체의 프로퍼티는 똑같이 동작하나 '삭제' 명령에 대해서는 전자는 삭제가 불가능하나 후자는 삭제가 가능하다. 즉, 전역변수와 전역객체의 프로퍼티는 호이스팅 여부 및 configurable 여부에서 차이가 있다.

### 메서드로서 호출할 때 그 메서드 내부에서의 this
>자바스크립트를 처음 접하는 분들은 흔히 메서드를 '객체의 프로퍼티에 할당된 함수'로 이해하곤 합니다. 반은 맞고 반은 틀립니다. 어떤 함수를 객체의 프로퍼티에 할당한다고 해서 그 자체로서 무조건 메서드가 되는 것이 아니라 객체의 메서드로서 호출할 경우에만 메서드로 동작하고, 그렇지 않으면 함수로 동작합니다.(p.70)
- **어떤 함수를 호출할 때 그 함수 이름(프로퍼티명) 앞에 객체가 명시돼 있는 경우에는 메서드로 호출한 것이고, 그렇지 않은 모든 경우에는 함수로 호출한 것이다.**
- 메서드로서 호출한 경우 호출 주체는 점 표기법의 경우 마지막 점 앞에 명시된 객체가 this가 된다.

### 함수로서 호출할 때 그 함수 내부에서의 this
- 어떤 함수를 함수로서 호출한 경우에는 this가 지정되지 않고 전역 객체(상위 객체 X)를 가리킨다.

하지만 호출 당시 주변 환경의 this를 그대로 상속받아 사용하고 싶다면? `변수`, `화살표 함수`, `call 메서드`, `apply 메서드`를 사용해서 명시적으로 this를 바인딩하면 된다.

### 콜백 함수 호출 시 그 함수 내부에서의 this
- 콜백 함수에서의  this는 '무조건 이거다!'라고 정의할 수 없다. 
- 콜백 함수의 제어권을 가지는 함수(메서드)가 콜백 함수에서의 this를 결정하며, 특별히 정의하지 않은 경우에는 전역객체를 참조한다.
```Javascript
[1,2,3,4,5].forEach(function(x){
	console.log(this,x); //전역객체와 배열의 각요소
});

document.body.innerHTML +='<button id="a">클릭</button>';
document.body.querySelector('#a').addEventListener('click',function(e){
	console.log(this,e); //앞서 지정한 엘리먼트와 클릭 이벤트에 관한 정보가 담긴 객체
});
```

### 생성자 함수 내부에서의 this
객체지향 언어에서는 생성자를 클래스class, 클래스를 통해 만든 객체를 인스턴스instance라고 한다.
생성자 함수: new 명령어와 함께 함수를 호출하면 생성자로서 동작하는 함수이다.

- 어떤 함수가 생성자 함수로서 호출된 경우 내부에서의 this는 곧 새로 만들 구체적인 인스턴스 자신이 된다.

## 02 명시적으로 this를 바인딩하는 방법
### call 메서드
- 메서드의 호출 주체인 함수를 즉시 실행하도록 하는 명령이다. 
- 메서드의 첫 번째 인자를 this로 바인딩하고, 이후의 인자들을 호출할 함수의 매개변수로 한다. 그냥 실행하면 (전역)객체를 참조한다

### apply 메서드
- call 메서드와 완전히 동일한 기능을 한다. 
- 첫 번째 인자를 this로 바인딩하고 두 번째 인자를 배열로 받아 그 배열의 요소들을 호출할 함수의 매개변수로 지정한다.

### call / apply 메서드의 활용
1. 유사배열객체에는 call 또는 apply 메서드를 이용해 배열 메서드를 차용할 수 있다.
2. 문자열에 대해서도 call/apply 메서드를 이용해 배열 메서드를 적용할 수 있다.
- 하지만 문자열의 length 프로퍼티는 읽기 전용이므로 원본 문자열에 변경을 가하는 메서드(ex. push, pop, shift, unshift, splice 등)에서는 에러가 발생하고, concat을 사용하면 에러는 나지 않지만 제대로 된 결과를 얻을 수 없다.
```Javascript
var str = 'abc def';
Array.prototype.concat.call(str, 'string'); // [String ["abc def"], "string"]

var newArr = Array.prototype.map.call(str, function(char) { return char + '!';});
console.log(newArr); //['a!', 'b!', 'c!', ' !', 'd!', 'e!', 'f!']

var newStr = Array.prototype.reduce.apply(str, [
	function(string, char, i) { return string + char + i;},''
]);
console.log(newStr); // "a0b1c2 3d4e5f6
```
ES6에서는 유사배열객체 또는 순회 가능한 모든 종류의 데이터 타입을 배열로 전환하는 Array.from 메서드가 새로 도입됐다.
3. 생성자 내부에 다른 생성자와 공통된 내용이 있을 경우 call 또는 apply를 이용해 다른 생성자를 호출하여 반복을 줄일 수 있다.
4. 여러 개의 인수를 받는 메서드에게 하나의 배열로 인수를 전달할 수 있다.

### bind 메서드
- call과 비슷하지만 즉시 호출하지는 않고 넘겨받은 this 및 인수들을 바탕으로 새로운 함수를 반환하기만 한다.
- 다시 새로운 함수를 호출할 때 인수를 넘기면 그 인수들은 기존 bind 메서드를 호출할 때 전달했던 인수들의 뒤에 이어서 등록된다.
```Javascript
var func = function (a,b,c,d){
	console.log(this, a, b, c, d);
};
func(1,2,3,4);

var bindFunc1 = func.bind({x:1});
bindFunc1(5,6,7,8); // {x:1} 5 6 7 8

var bindFunc2 = func.bind({x:1}, 4,5);
bindFunc2(8,9); // {x:1} 4 5 8 9 
```

- 상위 컨텍스트의 this를 내부함수나 콜백 함수에 전달하기
```Javascript
var obj = {
    outer: function () {
        console.log(this); // {outer: f}
        var innerFunc = function () {
            console.log(this); // {outer: f}
        }.bind(this);
        innerFun();
    }
};
obj.outer();
```
콜백 함수를 인자로 받는 함수나 메서드 중에서 콜백 함수 내에서의 this에 관여하는 함수 또는 메서드에 대해서도 bind 메서드를 이용하면 this 값을 임의로 바꿀 수 있다.
```Javascript
var obj = {
	logThis: function () {
		 console.log(this);
	},
	logThisLater1: function () {
		 console.log(this.logThis,500);
	},
	logThisLater2: function () {
		 console.log(this.logThis.bind(this),1000);
	},
};
obj.logThisLater1(); // Window {...}
obj.logThisLater2(); // obj {logThis: f, ...}
```

### 화살표 함수의 예외사항
화살표 함수에서는 스코프 체인상 가장 가까운 this에 접근한다

### 별도의 인자로 this를 받는 경우(콜백 함수 내에서의 this)
콜백 함수를 인자로 받는 메서드 중 일부
- `forEach`, `map`, `filter`, `some`, `every`, `find`, `findIndex`, `flatMap`, `from`은 this로 지정할 객체를 인자로 받아와 콜백 함수 내부에서의 this 값을 원하는 대로 변경할 수 있다..

https://www.notion.so/Chapter-3-this-9c807ccda0a3473e885c8b99653af5f1
