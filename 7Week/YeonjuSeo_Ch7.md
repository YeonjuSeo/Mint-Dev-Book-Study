# 클래스

프로토타입 기반 언어인 자바스크립트는 '상속' 개념이 존재하지 않는다. 이는 클래스 기반의 언어에 익숙한 개발자들을 혼란스럽게 했고 클래스를 흉내내는 기법을 사용하다 ES6부터는 클래스 문법이 추가되었다.

이 챕터에서는 ES6 전부터 이용되던, 프로토타입을 이용한 클래스 개념을 알아본다.

# 01 클래스와 인스턴스의 개념 이해

인스턴스instance란 어떤 클래스의 속성을 지니는 실존하는 개체를 말한다. 즉, 어떤 클래스에 속한 개체는 그 클래스의 조건을 모두 만족하므로 그 클래스의 구체적인 예시가 인스턴스라고 할 수 있다. 

한 인스턴스는 하나의 클래스만을 바탕으로 만들어진다. 어떤 인스턴스가 다양한 클래스에 속할 수는 있지만 이 클래스들은 모두 인스턴스 입장에서는 '직계존속'이다. 다중상속을 지원하는 언어이든 그렇지 않은 언어이든 결국 인스턴스를 생성할 때 호출할 수 있는 클래스는 오직 하나뿐일 수 밖에 없기 때문이다.

# 02 자바스크립트의 클래스

자바스크립트에는 클래스의 개념이 존재하지 않으나 프로토타입 체이닝에 의한 prototype 객체 내부 요소들을 인스턴스에서 '참조'하는 것을 prototype 객체 내부 요소들이 인스턴스에 '상속'된다고도 볼 수 있다. 

하지만 내부 프로퍼티 중 prototype 프로퍼티를 제외한 나머지는 인스턴스에 상속되지 않는다

인스턴스에 상속되는지(인스턴스가 참조하는지) 여부에 따라 직접 접근이 불가능한 스태틱 멤버static member와 직접 접근이 가능한 인스턴스 멤버instance member로 나뉜다. *멤버는 변수와 메소드로 구성되어있다.

이 중 '인스턴스 메서드'는 인스턴스에서도 직접 메서드를 정의할 수 있는 자바스크립트의 경우에는 프로토타입에 정의한 메서드를 지칭하는 것인지 인스턴스에 정의한 메서드를 지칭하는 것인지 헷갈릴 수 있으므로 프로토타입 메서드prototype method라고 부른다.

인스턴스에서 직접 접근할 수 없는 메서드를 스태틱 메서드라고 한다. 스태틱 메서드는 생성자 함수를 this로 해야만 호출할 수 있다.

구체적인 인스턴스가 사용할 메서드를 정의한 '틀'의 역할을 담당하는 목적을 가질 때의 클래스는 추상적인 개념이지만, 클래스 자체를 this로 해서 직접 접근해야만 하는 스태틱 메서드를 호출할 때의 클래스는 그 자체가 하나의 개체로서 취급된다.

# 03 클래스 상속

### 기본 구현

목표: 프로토타입 체인을 활용해 클래스 상속을 구현하고 최대한 전통적인 객체지향 언어에서의 클래스와 비슷한 형태로까지 발전시켜 보기.

```jsx
var Rectangle = function (width, height) {
	this.width = width;
	this.height = height;
};

Rectangle.prototype.getArea = function () {
	return this.width * this. height;
};

var rect = new Rectangle(3,4);
console.log(rect.getArea()); //12

var Square = function (width){
	Rectangle.call(this, width, width);
};

Square.prototype.getArea = function () {
	return this.width * this.height;
};

Square.prototype = new Rectangle;

var sq = new Square(5);
console.log(sq.getArea()); //25
```

위와 같은 코드는 Square가 Rectangle을 상속한 것처럼 동작하나,

1. 클래스에 있는 값이 인스턴스에 영향을 줄 수 있으며 
2. constructor가 Square가 아닌 Rectangle을 가리키는 문제

가 수반된다.

### 클래스가 구체적인 데이터를 지니지 않게 하기

: 만들고 나서 프로퍼티를 일일이 지우고 새로운 프로퍼티를 추가할 수 없도록 하기

- 인스턴스 생성 후 프로퍼티 제거
- 빈 함수(Bridge)를 활용

```jsx
var extendClass2 = (function () {
	var Bridge = function () {};
	return function (SuperClass, SubClass, subMethods){
		Bridge.prototype = SuperClass.prototype;
		SubClass.prototype = new Bridge();
		if(SubMethods){
			for(var method in subMethods){
				SubClass.prototype[method] = subMethods[method];
			}
		}
		Object.freeze(SubClass.prototype); //더이상 새로운 프로퍼티 추가할 수 없도록
		return SubClass;
	}
})
```

⇒ Rectangle의 자리를 Bridge가 대체하게 되어 인스턴스를 제외한 프로토타입 체인 경로 상에는 더이상 구체적인 데이터가 남아있지 않게 된다.

- Object.create 활용

```jsx
Square.prototype = Object.create(Rectangle.prototype);
Object.freeze(Square.prototype);
```

### constructor 복구하기

: 위 코드에서 SubClass.prototype.constructor가 원래의 SubClass를 바라보도록 하기

```jsx
var extendClass3 = function (SuperClass, SubClass, subMethods) {
	SubClass.prototype = Object.create(SuperClass.prototype);
	SubClass.prototype.constructor = SubClass;
	if(subMethods){
		for(var method in subMethods){
			SubClass.prototype[method] = subMethods[method];
		}
	}
	Object.freeze(SubClass.prototype);
	return SubClass.\;
}
```

# 04 ES6의 클래스 및 클래스 상속

### 클래스 문법

```jsx
var ES6 = class {
	constructor (name) { //(new) es5에서의 생성자 함수와 동일한 역할
		this.name = name;
	}
	static staticMethod () {
		return this.name + ' staticMethod';
	}
//(new)자동으로 prototype 객체 내부에 할당되는 메서드
**//** 인스턴스가 프로토타입 체이닝을 통해 자신의 것처럼 호출 가능
	method () {
		return this.name + ' method';
	}
};

var es6Instance = new ES6('es6');
console.log(ES6.staticMethod());
console.log(es6Instance.method()); 
```

### 클래스 상속

```jsx
var Rectangle = class {
	constructor (width, height) {
		this.width = width;
		this.height = height;
	}
	getArea() {
		return this.width * this.height;
	}
};

var Square = class extends Rectangle { //extends를 이용해서 상속 관계 설정
	constructor (width){
		super(width, width);
	}
	getArea(){
		console.log('size is: ', super.getArea());
		// 호출한 메서드의 this는 'super'가 아닌 원래의 this를 그대로 따른다.
	}
};
```
