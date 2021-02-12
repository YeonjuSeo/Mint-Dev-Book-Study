# Chapter 5 클로저

## 01 클로저의 의미 및 원리 이해
- 클로저는 **어떤 함수에서 선언한 변수를 참조하는 내부함수에서만 발생하는 현상**이다.
- 어떤 함수에서 선언한 변수를 차조하는 내부함수에서만 발생하는 현상이란 **외부 함수의 LexicalEnvironment가 가비지 컬렉팅되지 않는 현상**을 말한다. </br>

➡ 클로저란 **어떤 함수 A에서 선언한 변수 a를 참조하는 내부함수 B를 외부로 전달할 경우 A의 실행 컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상**을 말한다.

## 02 클로저와 메모리 관리
클로저는 의도적으로 함수의 지역변수를 메모리를 소모하도록 함으로써 발생한다. 
이러한 메모리 소모를 관리하려면 그 필요성이 사라진 시점에는 더는 메모리를 소모하지 않도록 식별자에 참조형이 아닌 기본형 데이터(보통 null이나 undefined)를 할당하여 참조 카운트를 0으로 만들어 소모됐던 메모리가 회수될 수 있도록 하면 된다.

## 03 클로저 활용 사례

### 콜백 함수 내부에서 외부 데이터를 사용하고자 할 때
- 콜백 함수를 내부함수로 선언해서 외부변수를 직접 참조
- bind 메서드로 값을 직접 넘겨주어 클로저 사용 없이 외부 데이터를 사용
- 콜백 함수를 고차함수로 바꾸어 클로저를 활용

### 접근 권한 제어(정보 은닉)
정보 은닉은 어떤 모듈의 내부 로직에 대해 외부로의 노출을 최소화해서 모듈 간의 결합도를 낮추고 유연성을 높이고자 사용한다.
```Javascript
var outer = function () {
	var a = 1;
	var inner = function () {
		return ++a;
	};
	return inner;
};
var outer2 = outer();
console.log(outer2()); //2
console.log(oouter2()); //3
```
클로저를 활용하여 접근권한을 제어하려면
```
1. 함수에서 지역변수 및 내부함수 등을 생성한다.
2. 외부에서 접근권한을 주고자 하는 대상들로 구성된 참조형 데이터(대상이 여럿일 때는 개체 또는 배열, 하나일 때는 함수)를 return한다.
```
→ return한 변수들은 공개 멤버가 되고, 그렇지 않은 변수들은 비공개 멤버가 된다.

### 부분 적용 함수
부분 적용 함수partially applied function란 n개의 인자를 받는 함수에 미리 m개의 인자만 넘겨 기억시켰다가, 나중에 (n-m)개의 인자를 넘기면 비로소 원래 함수의 실행 결과를 얻을 수 있게끔 하는 함수이다.
ex. 디바운스: 짧은 시간 동안 동일한 이벤트가 많이 발생할 경우 이를 전부 처리하지 않고 처음 또는 마지막에 발생한 이벤트에 대해 한 번만 처리하는 기능
```Javascript
var debounce = function (eventName, func, wait){
	var timeoutId = null;
	return function (event){
		var selt = this;
		console.log(eventName, 'event 발생');
		clearTimeout(timeoutId);
		timeoutId = setTimeout(func.bind(self, event), wait);
	};
};

var moveHandler = function (e){
	console.log('move event 처리');
};

var wheelHandler = function (e){
	console.log('wheel event 처리');
};

document.body.addEventListener('mousemove', debounce('move', moveHandler, 500));
document.body.addEventListener('mousewheel', debounce('move', wheelHandler, 700));
```
여기서 클로저로 처리되는 변수는 eventName, func, wait, timeoutId이다.

### 커링 함수
커링 함수currying function란 여러 개의 인자를 받는 함수를 하나의 인자만 받는 함수로 나눠서 순차적으로 호출될 수 있게 체인 형태로 구성한 함수이다.
- 반드시 한 번에 하나의 인자만 전달한다.
- 중간 과정상의 함수를 실행한 결과는 그다음 인자를 받기 위해 대기하므로 마지막 인자가 전달되기 전까지는 원본 함수가 실행되지 않는다.
```Javascript
var curry3 = function (func){
	return function (a){
		return function(b){
			return func(a,b);
		};
	};
};
//ES6의 화살표 함수로 작성해보면
//var curry3 = func => a => b => func(a,b);

var getMaxWith10 = curry3(Math.max)(10);
console.log(getMaxWith10(8)); //10
console.log(getMaxWith10(25)); //25
```

https://www.notion.so/Chapter-5-5fbae32a2f9349bfa77cbd2f2c72b2f4
