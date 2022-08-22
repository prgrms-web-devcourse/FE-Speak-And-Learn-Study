# 참고
[모던 자바스크립트 Deep Dive](https://poiemaweb.com/js-this)  
[strict mode - MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Strict_mode)
[화살표 함수 - MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

# this
함수를 호출 시, 우리가 직접 전달하는 `arguments`들 외에도 암묵적으로 `this`를 전달하게 된다. 아마 일반적인 다른 언어에서는 `this(self)`는 인스턴스 자신을 가리키는 참조 변수일 것이다. 그러나 자바스크립트에서 `this`는 다른 언어와는 달리 호출 주체가 누구인지, 함수의 호출 방식에 따라 달라진다.

## 예상 질문
### 1. 자바스크립트에서 this는 다른 언어와 어떤 차이점이 있나요?

자바스크립트에서 `this`는, 다른 언어와는 달리 그 값이 함수가 호출되는 시점에 결정된다는 것입니다. 따라서 호출 방식에 따라 `this`가 가지는 의미는 유동적입니다. 참고로, 자바에서는 인스턴스 자신을 가리킵니다.

### 1. 호출 방식에 따라 어떤 값을 가질 수 있나요?
일반적인 함수 `function` 키워드로 호출할 경우, 일반적으로 `this`는 `window`를 가리킵니다. 다만, `new` 키워드를 통해 생성자 함수로 호출한다면, `this`는 객체 인스턴스 자신을 가리킵니다. 객체의 메소드에서 호출하는 경우도 동일하게 객체의 인스턴스를 가리킵니다.  
`apply/call/bind`함수를 통하여 `this` 값을 임의로 바인딩할 수 있으므로, 이때는 바인딩한 값을 가리키게 됩니다.  
Arrow Function은 `this`를 바인딩하지 않습니다. 따라서 `apply/call/bind` 함수를 통해 `this`를 바인딩할 수 없고, 무조건 상위 스코프의 `this` 값을 따릅니다.  
마지막으로 `use strict`(엄격 모드)에서 `this`는, 직접 `this`를 명시하거나 바인딩하지 않는다면 `undefined`를 출력합니다.

<!--TODO: Modiy it-->
### 2. `call/apply/bind`의 역할은 무엇인가요?
`call/apply/bind`의 세부 호출 방식은 조금씩 다르지만, 원리는 동일합니다. `function` 키워드로 호출된 함수의 `this`를 명시적으로 바인딩할 수 있습니다. 그리고 모든 `call/apply/bind` 메소드를 호출하는 주체는 함수이며 this를 특정 객체에 바인딩 할 뿐 본질적인 목적은 함수 호출이라는 것입니다.

#### apply
- `func.apply(thisArg, [argsArray])`는 첫 번째 인자로는 this를 바인딩할 인스턴스, 두 번째 인자는 필요한 매개변수들을 배열 형식으로 전달하여 인스턴스를 바인딩합니다.

```typescript
var Person = function(name) {
  this.name = name;
}
var foo = {};

// person 함수의 this값을 foo 인스턴스로 바인딩 한 뒤, 매개변수에 'name'을 전달한다.
Person.apply(foo, ['name'])

console.log(foo); // {name: name}
```
  - apply 메소드의 대표적인 용도는 유사 배열 객체인 arguments를 배열 형태로 나타낼 경우입니다. 

```typescript
function convertArgsToArray(...args) {
	console.log(arguments)
  const arr = Array.prototype.slice.apply(arguments)
  console.log(arr)
  return arr
}

convertArgsToArray(1, 2, 3)
```
유사 배열 객체인 `arguments`는 다음의 형태를 띄고 있습니다.
```
[object Arguments] {
  0: 1,
  1: 2,
  2: 3
}
```
`Array.prototpye.slice`메소드를 호출할 때, `apply` 함수로 `this`를 `arguments`로 바인딩하게 됩니다. 따라서 `Array.prototype.slice`메소드를 arguments 자신의 메소드인 것처럼 `arguments.slice()` 형태로 호출 할 수 있게 됩니다.

#### call
- call은 `this`를 임의 인스턴스로 바인딩하는 기능은 동일하지만, `apply`가 두 번째 인자로 배열 형태의 `arguments`를 매개변수로 전달 받는 것과는 달리, 배열 형태가 아닌, 필요한 각각의 인자를 하나씩 넘깁니다.

```typescript
// 동일한 기능을 수행한다.
Person.apply(lee, [1, 2, 3])
Person.call(lee, 1, 2, 3)
```

#### bind
- bind는 ES5에서 새롭게 추가된 것으로, `this`를 임의의 값으로 바인딩 하는 일은 동일하지만, `call`, `apply`와는 달리 `this`가 바인딩된 새로운 함수를 반환하게 됩니다.

```typescript
function Person(name) {
  this.name = name;
}
Person.prototype.doSomething = function (cb) {
  if (typeof cb === 'function') {
    cb.bind(this)(); // 기본적으로 함수를 반환하므로 즉시 실행하기 위해서는 함수를 호출해야 함
  }
}

function foo() {
  console.log(this.name);
}

const p = new Person('lee')
p.doSomething(foo); // lee
```

`call/apply/bind`는 일반 함수로 호출 시 기본적으로 `window` 객체를 가리키는 자바스크립트 함수 특성 상, 콜백 함수에서 상위 스코프의 인스턴스를 처리하기 위하여 주로 쓰였습니다.

### 3. 화살표 함수를 통한 정적 바인딩이 좋은 방법인가요? 상황별로 다르다면, 어떤 상황에서 화살표 함수를 통해 this를 정적 바인딩 하는 것이 좋고, 어떤 상황에서 좋지 않을까요?

**좋을 수도, 좋지 않을 수도 있습니다.** 화살표 함수의 가장 큰 특징은 this를 바인딩하지 않습니다. 따라서 `this`가 함수 내부에 존재하면, `this`를 바인딩한 함수를 찾을 때까지 스코프를 거슬러 올라가게 됩니다. 만약 우리가 callback 함수로 무엇인가를 작성할 때, 상위 스코프의 `this`를 가지고 후속 처리를 해야한다면, 화살표 함수가 더 좋은 선택일 수 있습니다. 왜냐하면 일반 함수와 달리 바인딩 과정을 거쳐도 되지 않기 때문에 복잡한 절차를 거치지 않아도 되고, 이해도 쉬울 수 있기 때문입니다.
화살표 함수는 `this`와 `super`를 바인딩하지 않으므로, class의 constructor로 사용될 수 없고 함수의 메소드로 사용또한 어렵습니다.  
그리고 어떤 임의의 iterator를 만들고 호출하기 위하여 `yield`예약어를 사용해야 한다면, 화살표 함수 스코프에서는 사용이 불가능하므로 일반 함수를 통해 `iterator`를 작성해야 합니다.