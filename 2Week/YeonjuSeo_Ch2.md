# Chapter 2 실행 컨텍스트
실행 컨텍스트execution context란? 실행할 코드에 제공할 환경 정보들을 모아놓은 객체

## 목표
실행 컨텍스트를 정확히 이해하기

## 01 실행 컨텍스트란?
- 우리가 하나의 실행 컨텍스트를 구성하는 방법은 **함수를 실행**하는 것 뿐이다.
- 한 실행 컨텍스트가 콜 스택의 맨 위에 쌓이는 순간이 곧 현재 실행할 코드에 관여하게 되는 시점이다.
- 실행 컨텍스트 객체에 담기는 정보는 아래와 같다.
  - VariableEnvironment: environmentRecord(매개변수명, 변수의 식별자, 선언한 함수의 함수명 등) + outer-EnvrionmentReference(바로 직전 컨텍스트의 LexicalEnvironment 정보를 참조)
  - LexicalEnvironment: VariableENvironment와 동일한 내용 + 실시간으로 반영되는 변경 사항
  - ThisBinding: this 식별자가 바라봐야 할 대상 객체
  
## 02 VariableEnvironment
- LexicalEnvironment와 달리 최초 실행 시의 스냅샷을 유지한다.

## 03 LexicalEnvironment
- 함수 실행 도중 변경되는 사항이 variableEnvironment와 달리 즉시 반영된다.
### environmentRecord와 호이스팅
- 코드가 실행되기도 전에 environmentRecord는 현재 컨텍스트와 관련된 코드의 식별자 정보를 컨텍스트 내부에서 **순서대로** 수집한다.

그러므로 "자바스크립트 엔진은 식별자들을 최상단으로 끌어올려놓은 다음(= hoisting) 실제 코드를 실행한다"라고 생각해도 코드를 해석하는 데는 문제될 것이 없다.
### 호이스팅 규칙
- 매개변수나 변수: 선언부만 끌어올리고 할당부는 원래 자리에 남겨둔다.
- 함수 선언: 함수 전체를 끌어올린다.
- 호이스팅이 끝난 상태에서의 함수 선언문은 함수명으로 선언한 변수에 함수를 할당한 것처럼 여길 수 있다.
--------
```javascript
function a(){
	console.log(b);
	var b= 'bbb';
	console.log(b);
	function b(){}
	console.log(b);
}
a();
```
```javascript
function a(){
	var b;
	functino b() {}
	
	console.log(b);
	b='bbb';
	console.log(b);
	console.log(b);
}
a();
```
```javascript
function a(){
	var b;
	var b = function b (){}
	
	console.log(b);
	b='bbb';
	console.log(b);
	console.log(b);
}
a();
```
*b함수 'bbb', 'bbb'가 차례로 출력된다.
###함수 선언문과 함수 표현식
- 함수 선언문: function 정의부만 존재하고 별도의 할당 명령이 없는 것. 반드시 함수명이 정의되어 있어야 한다.
```javascript
fucntion sum(a,b){
	return a+b;
}
- 함수 표현식: 함수를 다른 변수에 값으로써 **할당**한 것
  - 기명 함수 표현식: 함수명을 정의한 함수 표현식
  ```javascript
  var sum = fucntion sum(a,b){
	return a+b;
  };
  ```
  - 익명 함수 표현식: 함수명을 정의하지 않은 함수 표현식(일반적으로 말하는 함수 표현식은 익명이다.)
  
  ```javascript
  var multiply = function (a,b){
	return a*b;
  };
  ```
  
  *3, multiply is not a function이 차례로 출력된다.
  ### 스코프, 스코프 체인, outerEnvironmentReference
  - 스코프: 식별자에 대한 유효범위.
  - 스코프 체인: '식별자의 유효범위'를 안에서부터 바깥으로 차례로 검색해나가는 것. <br/>
  스코프 체이닝을 가능하게 하는 것이 바로 outerEnvironmentReference이다.
  -------------
  - outerEnvironmentReference는 자신이 선언된 시점의 LexicalEnvironment만 참조하고 있으므로 가장 가까운 요소부터 차례대로만 접근할 수 있다.
  - 변수 은닉화variable shadowing:<br/>
  여러 스코프에서 동일한 식별자를 선언한 경우 가장 먼저 발견된 해당 식별자만 반환한 뒤 스코프 체인 검색을 멈춘다. 
  이로 인해 더 외부에 선언한 동일한 이름의 해당 변수에는 접근할 수 없다.
  - 전역 컨텍스트의 LexicalEnvironment까지 탐색해도 해당 변수를 찾지 못하면 undefined를 반환한다.
  ## 04 this
  - 실행 컨텍스트 활성화 당시에 this가 지정되지 않은 경우 this에는 전역 객체가 저장된다.
  <br/><br/><br/>

https://www.notion.so/Chapter-2-da433b4180b74a4694db95d59a75c5d9
