# Chapter 6 프로토타입
자바스크립트는 프로토타입 기반 언어이다. 프로토타입 기반 언어에서는 어떤 객체를 원형prototype으로 삼고 이를 복제(참조)함으로써 상속과 비슷한 효과를 얻는다.

## 01 프로토타입의 개념 이해

### constructor, prototype, instance
> - 어떤 생성자 함수Constructor를 new 연산자와 함께 호출하면
> - Constructor에서 정의된 내용을 바탕으로 새로운 인스턴스instance가 생성된다.
> - 이때 instance에는 __proto__라는 프로퍼티가 자동으로 부여되는데,
> - 이 프로퍼티는 Constructor의 prototype이라는 프로퍼티를 참조한다. <br/> 
    -> Constructor.prototype === instance.__proto__ //true

```Javascript
var Person = function (name) {
	this._name = name;
};
Person.prototype.getName = function () {
	return this._name;
};

var suzi = new Person('Suzi');
suzi.__proto__.getName(); //undefined
```
메서드 호출 시 this로 메서드 앞의 객체가 바인딩되므로 undefined가 출력된다. <br/>
this에 우리의 의도대로 바인딩을 할 수 있는 방법은 proto 없이 인스턴스에서 곧바로 메서드를 호출하는 것이다. 왜냐하면 '__proto__'는 **생략가능**한 프로퍼티이기 때문이다. 

자바스크립트는 함수에 자동으로 객체인 prototype 프로퍼티를 생성해 놓는데, 해당 함수를 생성자 함수로서 사용할 경우, 생성된 인스턴스에는 생략 가능한 프로퍼티인 '__proto__'가 자동으로 생성되므로 **생성자 함수의 prototype에 어떤 메서드나 프로퍼티가 있다면 인스턴스에서도 자유롭게 해당 메서드나 프로퍼티에 접근할 수 있게 된다.**

### constructor 프로퍼티
생성자 함수의 프로퍼티인 prototype 객체와 인스턴스의 '__proto__' 객체 내부에는 constructor라는 프로퍼티가 있다. 이는 인스턴스로부터 그 원형이 무엇인지를 알 수 있는 수단이 된다.

- 아래는 모두 동일한 대상을 가리킨다.
```
- [Constructor]
- [instance].__proto__.constructor
- [instance].constructor
- Object.getPrototypeOf([instance]).constructor
- [Constructor].prototype.constructor
```

- 아래는 모두 동일한 객체(prototype)에 접근할 수 있다.
```
- [Constructor].prototype
- [instance].__proto__
- [instance]
- Object.getPrototypeOf([instance])
```

## 02 프로포타입 체인

### 메서드 오버라이드
- 메서드 오버라이드란 인스턴스가 prototye에 있는 것과 동일한 이름의 프로퍼티 또는 메서드를 가지고 있는 상황에서 원본이 그대로 있는 상태에서 다른 대상을 그 위에 얹는(덮어씌우는) 현상이다.
- 자바스크립트 엔진이 메서드를 찾는 방식은 가장 가까운 대상인 자신의 프로퍼티를 검색하고, 없으면 그다음으로 가까운 대상인 '__proto__'를 검색하는 순서로 진행한다. 즉 '__proto__'에 있는 메서드는 자신에게 있는 메서드보다 검색 순서에서 밀려 호출되지 않은 것이다.
- 메서드 오버라이딩이 이뤄져 있는 상황에서 prototype에 있는 메서드에는 call이나 apply를 이용하여 우회적으로 접근이 가능하긴 하다.

### 프로토타입 체인
- 프로토타입 체인prototype chain이란 어떤 데이터의 '__proto__' 프로퍼티 내부에 다시 '__proto__' 프로퍼티가 연쇄적으로 이어진 것을 말한다.
- 프로토타입 체인을 따라가며 검색하는 것을 프로토타입 체이닝prototype chaining이라고 한다. 방식은 메서드 오버라이딩과 동일하다.
- prototype 객체는 '객체'이므로 자바스크립트 데이터는 모두 Object.prototype에 도달이 가능하다.

### 객체 전용 메서드의 예외사항
- Object.prototype이 여타의 참조형 데이터뿐 아니라 기본형 데이터조차 '__proto__'에 반복 접근함으로써 도달할 수 있는 최상위 존재이기때문에 객체 한정 메서드들은 Object.prototype이 아닌 Object에 직접 부여해야만 한다. 
- 같은 이유에서, Object.prototype에는 어떤 데이터에서도 활용할 수 있는 범용적인 메서드들만이 있다.

### 다중 프로토타입 체인
자바스크립트의 기본 내장 데이터 타입들은 모두 프로토타입 체인이 1단계(객체)이거나 2단계(나머지)로 끝나는 경우만 있지만 사용자가 직접 '__proto__'가 가리키는 대상, 즉 생성자 함수으 prototype이 연결하고자 하는 상위 생성자 함수의 인스턴스를 바라보게끔 해주면 그 이상의 단계가 이어지는 프로토타입 체인도 가능하다.

```Javascript
var Grade = function () {
	var args = Array.prototype.slice.call(arguments);
	for(var i = -; i < args.length; i++) {
		this[i] = args[i];
	}
	this.length = args.length;
};
var g= new Grade(100,80);
```
변수 g는 Grade의 인스턴스를 바라본다. 하지만 배열의 메서드를 사용할 수 없는 유사배열객체이다.  인스턴스에서 배열 메서드를 직접 쓸 수 있도록 하려면 g.__proto__, 즉 Grade.prototype이 배열의 인스턴스를 바라보게 한다.
```Javascript
Grade.prototype = [];
```
이젠 Grade의 인스턴스인 g에서 직접 배열의 메서드를 사용할 수 있다.

https://www.notion.so/f03d3ee1a78347a185dd004435438ca5
