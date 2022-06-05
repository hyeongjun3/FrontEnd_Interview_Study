# [Web] Proxy and Meta programming

ES6 `Proxies` 는 객체의 operations들을 가로채어서 수정할 수 있는 기능을 제공해줍니다. 이러한 특징은 메타프로그래밍 의 특징입니다.

먼저 `Proxies` 에 대해서 공부하기 전에 메타프로그래밍 에 대해서 알아보겠습니다.

## 메타프로그래밍?

메타프로그래밍이란 자기 자신 혹은 다른 컴퓨터 프로그램을 데이터로 취급하여 프로그램을 실행할 수 있는 것을 말합니다.

메타프로그래밍에는 2가지 레벨이 존재합니다

- base level: application level이라고도 불리며 user input을 실행합니다
- meta level: base level code를 실행합니다.

base와 meta level 언어는 다를 수 있습니다. JavaScript가 meta level 언어, Java가 base level 언어인 아래 예제를 보겠습니다.

```jsx
let str = 'Hello' + '!'.repeat(3);
console.log('System.out.println("'+str+'")');
```

console.log 메소드가 자바의 System.out.println을 호출하였습니다.

## 메타프로그래밍 종류

`Reflective 메타프로그래밍` 은 한 프로그래밍 언어가 자기 자신의 메타 언어가 되는 것을 뜻합니다. 그리고 Reflective 메타프로그래밍은 아래 3가지로 분류 할 수 있습니다

- Introspection : 실행 시간에 메타 데이터를 읽는 기능만을 가지고 있습니다
- Self-modification : 메타 데이터를 스스로 변경할 수 있는 기능을 가지고 있습니다
- Intercession : 특정 메소드를 수행하는 일부를 재정의할 수 기능을 가지고 있습니다.

각각의 예제를 살펴보겠습니다

### Introspection

base와 meta level 언어가 JavaScript로 같을 때의 예제를 보겠습니다.

```jsx
// Base level
let obj = {
    hello() {
        console.log('Hello!');
    }
};

// Meta level
for (let key of Object.keys(obj)) {
    console.log(key);
}
```

위 예제에서 for loop 안에서 obj의 속성을 읽을 수 있습니다

### Self-modification

```jsx
function moveProperty(source, propertyName, target) {
    target[propertyName] = source[propertyName];
    delete source[propertyName];
}
```

```jsx
> let obj1 = { prop: 'abc' };
> let obj2 = {};
> moveProperty(obj1, 'prop', obj2);

> obj1
{}
> obj2
{ prop: 'abc' }
```

`moveProperty` 메소드는 source 객체의 property를 target 객체로 옮기고 source 객채의 property를 삭제합니다.

### Intercession

JavaScript에서는 해당 기능을 제공하지 않습니다. 하지만 `Proxy` 와 `Reflect` 는 이 기능을 가능하게 합니다.

## Proxy

 `Proxies` 는 객체의 operations들을 가로채어서 수정할 수 있는 기능을 제공해줍니다.

### 사용법

`Proxy` 객체는 2개의 파라미터가 필요합니다.

- target: Intercession 처리를 해야하는 대상 객체
- handler:  Intercession에 사용될 핸들러(트랩)를 추가할 떄 사용되는 객체

예제를 통해서 감을 잡아보겠습니다.

```jsx
var target = {};

var superhero = new Proxy(target, {
    get: function(target, name, receiver) {
        console.log('get was called for:', name);
        return target[name];
    }
});

superhero.power = 'Flight';
console.log(superhero.power);
```

원본 객체의 get 메소드를 가로채어서 로그 출력 후 get 메소드를 다시 실행하는 코드입니다.

Proxy는 위의 예시처럼 간단하게 사용할 수 있습니다.

그럼 Proxy는 어떻게 사용될 수 있을까요? 

Proxy는 다양하게 사용될 수 있습니다.

- Interception
- Object virtualization
- Resource management
- Profiling or logging for debugging
- Security and access control
- Contracts for object use

### Interception

위의 예시도 interception의 예제입니다. 이번에는 get 메소드가 아닌 특정 메소드를 가로채보겠습니다.

```jsx
// Proxying a function object
function sum(a, b) {
    return a + b;
}

var handler = {
    apply: function(target, thisArg, argumentsList) {
        console.log(`Calculate sum: ${argumentsList}`);
        return target.apply(thisArg, argumentsList);
    }
};

var proxy = new Proxy(sum, handler);
proxy(1, 2);
// Calculate sum: 1, 2
// 3
```

위와 다른점은 handler에서 `sum` 이 아닌 `apply` 를 사용했다는 점 입니다. 함수 `sum` 과 property `sum` 을 구분할 수 없기 때문에 function call의 경우에는 `apply` 를 사용합니다.

### Validation

```jsx
let validator = {
  set(obj, prop, value) {
    if (prop === 'age') {
      if (!Number.isInteger(value)) {
        throw new TypeError('The age is not an integer');
      }
      if (value > 200) {
        throw new RangeError('The age seems invalid');
      }
    }

    // The default behavior to store the value
    obj[prop] = value;

    // Indicate success
    return true;
  }
};

const person = new Proxy({}, validator);

person.age = 100;
console.log(person.age); // 100
person.age = 'young';    // Throws an exception
person.age = 300;        // Throws an exception
```

`Proxy` 를사용하여 객채에 전달되는 값을 검증할 수 있습니다.

### Access Control

```jsx
function NOPE() {
  throw new Error("can't modify read-only view");
}

var handler = {
  // Override all five mutating methods.
  set: NOPE,
  defineProperty: NOPE,
  deleteProperty: NOPE,
  preventExtensions: NOPE,
  setPrototypeOf: NOPE
};

function readOnlyView(target) {
  return new Proxy(target, handler);
}
```

해당 객체를 read-only 로 바꿀 수 있습니다

### Profiling or Logging

```jsx
const logger = []

const loggerHandler = {
  get: function (target, prop) {
    logger.push(`Someone  accessed '${prop}' on object ${target.name} at ${new Date()}`);
    return target[prop] || target.getItem(prop) || undefined;
  },
}

const secretProtectorHandler = {
  deleteProperty: function (target, prop) {
    // If the key we try to delete contains to substring 'secret' we don't allow the user to delete it
    if (prop.includes('secret')){
      return false;
    }
    return true;
  }
};

const sensitiveDataProxy = new Proxy (
  {name:'Secret JS Object', secretOne: 'I like weird JavaScript Patterns'},
  {...loggerHandler, ...secretProtectorHandler}
);

const {secretOne} = sensitiveDataProxy;
//logger = ['Someone tried to accessed 'secretOne' on object Secret JS Object at Mon Dec 09 2019 23:18:54 GMT+0900 (Japan Standard Time)']

delete sensitiveDataProxy.secretOne;
// returns false it can't be deleted!

// sensitiveDataProxy equals  {name: 'Secret JS Object', secretOne: 'I like weird JavaScript Patterns'}
```

## 참고자료

- [https://2ality.com/2014/12/es6-proxies.html](https://2ality.com/2014/12/es6-proxies.html)
- [https://developer.chrome.com/blog/es2015-proxies/](https://developer.chrome.com/blog/es2015-proxies/)
- [https://meetup.toast.com/posts/302](https://meetup.toast.com/posts/302)
- [https://hacks.mozilla.org/2015/07/es6-in-depth-proxies-and-reflect/](https://hacks.mozilla.org/2015/07/es6-in-depth-proxies-and-reflect/)
- [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)