# Chapter 4 콜백 함수

## 01 콜백 함수란?
콜백 함수callback function란 다른 코드(함수 또는 메서드)에게 인자로 넘겨줌으로써 그 제어권도 함께 위임한 함수이다. 
콜백 함수를 위임받은 코드는 자체적인 내부 로직에 의해 이 콜백 함수를 적절한 시점에 실행한다.

## 02 제어권
콜백 함수의 제어권을 넘겨받은 코드는 콜백 함수 호출 시점에 대한 제어권을 가집니다.

### 인자
콜백 함수의 제어권을 넘겨받은 코드는 콜백 함수를 호출할 때 인자에 어떤 값들을 어떤 순서로 넘길 것인지에 대한 제어권을 가진다.
- 콜백 함수를 호출하는 시점을 스스로 판단해서 실행한다.
- 콜백 함수를 호출할 때 인자로 넘겨줄 값들 및 그 순서가 정해져 있다. 

### this
콜백 함수도 함수이기 때문에 기본적으로는 this가 전역객체를 참조하지만, 
제어권을 넘겨받을 코드에서 콜백 함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조하게 된다.
```Javascript
Array.prototype.map = function(callback,thisArg){
	var mappedArr = [];
	for(var i=0; i<this.length; i++){
		var mappedValue = callback.call(thisArg || window, this[i], i, this); 
		//this에는 thisArg값이 있을 경우에는 그 값을, 없을 경우에는 전역객체를 지정한다.
		//그래서 this에 다른 값이 담기기도 한다.
		mappedArr[i] = mappedValue;
	}
	return mappedArr;
}
```

## 03 콜백 함수는 함수다
콜백 함수로 어떤 객체의 메서드를 전달하더라도 그 메서드는 메서드가 아닌 함수로서 호출된다.

## 04 콜백 함수 내부의 this에 다른 값 바인딩하기
별도의 인자로 this를 받는 함수의 경우에는 여기에 원하는 값을 넘겨주면 되지만 그렇지 않은 경우에는 this의 제어권도 넘겨주게 되므로 사용자가 임의로 값을 바꿀 수 없다.

- 전통적으로는 this를 다른 변수에 담아 콜백함수로 활용할 함수에서는 this 대신 그 변수를 사용하게 하고, 이를 클로저로 만드는 방식이 많이 쓰였다.
- ES5에서부터는 bind 메서드가 도입되었다.

## 05 콜백 지옥과 비동기 제어
콜백 지옥callback hell이란 콜백 함수를 익명 함수로 전달하는 과정이 반복되어 코드의 들여쓰기 수준이 감당하기 힘들 정도로 깊어지는 현상이다.
동기적인 코드란 현재 실행 중인 코드가 완료된 후에야 다음 코드를 실행하는 방식이다. 즉시 처리가 가능한 대부분의 코드는 동기적인 코드이다.
비동기적인 코드는 현재 실행 중인 코드의 완료 여부와 무관하게 즉시 다음 코드로 넘어가는 방식이다. 예를 들면
- 사용자의 요청에 의해 특정 시간이 경과되기 전까지 어떤 함수의 실행을 **보류**(setTimeout)
- 사용자의 직접적인 개입이 있을 때 비로소 어떤 함수를 **실행하도록 대기**(addEventListener)
- 웹브라우저 자체가 아닌 **별도의 대상에 무언가를 요청**하고 그에 대한 응답이 왔을 때 비로소 어떤 함수를 **실행하도록 대기**(XMLHttpRequest)

콜백 지옥을 해결하기 위한 방법으로는
- 익명의 콜백 함수를 모두 기명함수로 전환하기
- ES6에서 도입된 Promise, Generator
- ES2017에서 도입된 async/await
```Javascript
var addCoffee = function (name){
	return new Promise(function(resolve) {
		setTimeout(function () {
				resolve(name);
			},500);
	});
};
var coffeMaker = async function () {
	var coffeList = '';
	var _addCoffee =async function (name){
		coffeList += (coffeList ? ',':'') + await addCoffee(name);
	};
	await _addCoffee('에스프레소');
	console.log(coffeList);
	await _addCoffee('아메리카노');
	console.log(coffeList);
	await _addCoffee('카페모카');
	console.log(coffeList);
	await _addCoffee('카페라떼');
	console.log(coffeList);
};
coffeeMaker();
```

https://www.notion.so/Chapter-4-5d7e3249aca4491ebcf7a081653b4d61
