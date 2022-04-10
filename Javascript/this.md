**참고**
[모던 자바스크립트 Deep Dive](https://poiemaweb.com/js-this)
[strict mode - MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Strict_mode)

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

### 2. `call/apply/bind`의 역할은 무엇인가요?
`call/apply/bind`의 세부 호출 방식은 조금씩 다르지만, 원리는 동일합니다. `function` 키워드로 호출된 함수의 `this`를 임의로 바인딩하여 `this`가 바인딩한 객체를 가리키게 합니다.

