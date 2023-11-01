---
layout: post
title: 리액티브 프로그래밍, RxJs를 시작해보자.
date: 2020-08-24 10:18:00
categories: dev
---

# 리액티브 프로그래밍이란 ?

**리액티브 프로그래밍**은 데이터 흐름과 전달에 관한 **프로그래밍 패러다임**입니다.

기존 코드 형식은 개발자가 작성한 코드를 정해진 흐름따라 실행이 된다고 하면, 리액티브 프로그래밍은 데이터 흐름을 먼저 정의를 하고, 관찰을 하고 있다가 정의된 행동이 **감지**되면 실행합니다.

[리액티브 프로그래밍 공식 홈페이지](http://reactivex.io/documentation/observable.html) 에서 가져온 아래의 **마블 다이어그램**은 리액티브 프로그래밍의 동작을 표현하는 다이어그램입니다. 리액티브 프로그래밍의 동작을 확인하기 위해서는 마블 다이어그램이 사용되고 있습니다.

![legend.png](http://reactivex.io/assets/operators/legend.png)

1. 위 6개의 도형, 별, 삼각형, 오각형, 원, 사각형, 마름모는 Observable에서 **발행된 데이터**입니다.

2. **시간 순**으로 발행이 되며, **발행이 완료**( **파이프**(**|**) )되면 더이상 발행할 수 없습니다.

3. 발행된 데이터는 `flip()` 함수가 실행이 되어 **출력**이 되어집니다. (`flip함수`는 도형 위아래를 뒤집는 함수입니다.)

4. **에러**가 발생이 되면 **예외**가 발생하고 **종료**됩니다.



# 주요 구성 정리

리액티브 프로그래밍에선 아래와 같은 중요한 개념이 있습니다.
- **Observable**
- **Subscription**
- **Operators**
- **Subject**
- **Schedulers**  

## Observable

Observable은 문자 그대로 관찰할 수 있는 형태로 바꾸어 주는 역할을 하며 그 데이터를 Observer 클래스가 처리할 수 있도록 도와주는 클래스입니다. subscribe() 함수를 가지고 있습니다.

## Observer

Observer는 next(), error(), complete() 메서드를 가진 객체로, 버튼 클릭, HTTP요청 등 이벤트가 발생하였을때, 호출되어지며, Observable에서 데이터가 전달이 되면 그것들을 처리를 하는 클래스입니다.

## Subscription

Subscription은 Observable가 실행이 될 때, 생성되는 객체를 말합니다.

## Operators

Operation은 위 마블 다이어그램에서의 flip()함수와 같이 Observable내의 함수입니다. Operation은 종류가 많고 쓰임새도 다양합니다.

## Subject

Subject는 다중으로 처리가능한 Observable입니다. Subject클래스를 이용하여, 하나 이상의 Observer를 설정하여 다중으로 제어가 가능합니다. 

## Schedulers

자바스크립트는 기본적으로 싱글스레드, 이벤트 루프로 동작하는데 RxJs에서 Schedulers를 이용하여 이벤트 루프에 대한 처리순서를 제어할 수 있습니다.

# RxJs로 실습해보기

현재 Java,c++,javascript등 다양한 플랫폼이 리액티브 프로그래밍을 [지원](http://reactivex.io/languages.html)하고 있습니다.

그 중 실습해볼 언어는 **자바스크립트**입니다.



## 사전에 설치해야 할 것

[Node.js](https://nodejs.org/ko/)



## 환경구축 및 설치

### node프로젝트 생성

노드 프로젝트 생성을 위해 `npm init`로 초기화를 시켜줍니다.

```shell
$ npm init
```

위 명령어를 실행하면 **package.json**파일이 생성된 것을 확인할 수 있습니다.

```json
{
  "name": "rxjs-tutorial",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

### RxJs 설치

아래의 명령어를 실행하면 **package.json**에 **rxjs**가 추가된 것을 확인할 수 있습니다.

```shell
# RxJS 6버전이상을 설치할 것을 권장합니다. 5버전과는 문법상 상당한 차이가 있습니다.
$ npm install ---save-dev rxjs
```

**package.json**와 같은 디렉토리에 **index.js** 파일을 생성해줍니다.

###### index.js

```javascript
import { of } from 'rxjs';
import { map } from 'rxjs/operators';

map(x => x * x)(of(1, 2, 3)).subscribe((v) => console.log(`Output is: ${v}`));
```

 `node`를 이용해 실행해줍니다.

```shell
$ node index.js

import { of } from 'rxjs';
^^^^^^

SyntaxError: Cannot use import statement outside a module
    at wrapSafe (internal/modules/cjs/loader.js:1053:16)
    at Module._compile (internal/modules/cjs/loader.js:1101:27)
    at Module.load (internal/modules/cjs/loader.js:985:32)
    at Function.Module._load (internal/modules/cjs/loader.js:878:14)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:71:12)
    at internal/main/run_main_module.js:17:47
```

실행을 하면 위와 같이 import를 사용할 수 없다는 에러가 나는데, nodejs에서 import를 사용하기 위해선 **ES6 모듈**을 설치할 필요가 있습니다. 브라우저를 이용해서 확인을 할 수도 있고, 콘솔에서 바로 확인을 할 수 있습니다. **두가지 방법** 중 하나를 **선택**하여 진행합니다.



#### 1. 브라우저에서 실행

브라우저에서 ES6 모듈을 읽기 위해 번역과 번들링을 위해 **웹팩**과 **바벨**을 설치합니다.

```shell
$ npm install --save-dev babel-loader babel-core babel-preset-env
$ npm install --save-dev webpack webpack-cli webpack-dev-server
```

설치가 끝났다면 **웹팩**을 설정하기 위한, **webpack.config.js**과 **index.html**을 작성합니다.

###### webpack.config.js

```javascript
const path = require('path');

module.exports = {
  entry: './index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
    publicPath: '/dist/'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        include: [
          path.resolve(__dirname, 'src/js')
        ],
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
            plugins: ['@babel/plugin-proposal-class-properties']
          }
        }
      }
    ]
  },
  devtool: 'source-map',
  mode: 'development'
};
```

###### index.html

```html
<!DOCTYPE html>
<html>
<body>
  <script type="text/javascript" src="./dist/bundle.js"></script>
</body>
</html>
```

마지막으로 **package.json**의 **scripts**에 **webpack-dev-server**로 실행하기 위한 스크립트를 추가해줍니다.

```json
"dev": "webpack-dev-server --Host 0.0.0.0 --port 9000 --open --progress"
```

```json
# 설치 후 package.json
{
  "name": "rxjs-tutorial",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack -w",
    "dev": "webpack-dev-server --Host 0.0.0.0 --port 9000 --open --progress"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-core": "^6.26.3",
    "babel-loader": "^8.1.0",
    "babel-preset-env": "^1.7.0",
    "rxjs": "^6.6.2",
    "webpack": "^4.44.1",
    "webpack-cli": "^3.3.12",
    "webpack-dev-server": "^3.11.0"
  }
}
```

위에서 추가한 스크립트를 실행을 하여 서버를 가동합니다. 

```shell
$ npm run dev
```

[localhost:9000](http://localhost:9000)에 접속을 하면, 아래와 같이 콘솔창에서 로그가 출력이 된 것을 확인할 수 있습니다.

![2](http://localhost/content/images/2020/08/2.PNG)



#### 2. 콘솔에서 실행

ES6 모듈 설치를 위해 **esm**을 설치합니다.

```shell
$ npm install --save-dev esm
```

```json
# 설치 후 package.json
{
  "name": "rxjs-tutorial",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "esm": "^3.2.25",
    "rxjs": "^6.6.2"
  }
}
```

`node -r esm` 명령어를 통해 실행하게 되면 콘솔에서 로그를 확인할 수 있습니다.

```shell
$ node -r esm index.js  

Output is: 1
Output is: 4
Output is: 9
```



## RxJs 튜토리얼

1. **Observable**
2. **Operation**
3. **Subject**
4. **Schedulers**



### 1. Observable 

**Observable**는 생성자를 사용하거나, **create**를 이용하여 생성이 가능합니다.

##### index.js

```javascript
// 생성자를 이용한 생성 방법
import { Observable } from 'rxjs';

//let observer = Observable.create( ... 도 가능
let observer = new Observable(
   (subscriber) => {
      try {
         subscriber.next("메시지1");
         subscriber.next("메시지2");
         subscriber.complete();
         subscriber.next("메시지3");
      } catch(e){
         subscriber.error(e);
      }
   }
);
observer.subscribe(x => console.log(x), e => console.log(e), () => console.log('발행완료'));
```

위 코드로 observer라는 Observable클래스의 객체를 생성하고, 아규먼트에 **subscriber.next**메서드를 사용하여 메시지를 발행하고 **subscriber.complete**를 이용하며 발행을 완료시킵니다. 그 후  **subscribe**를 이용하여 발행한 데이터를 콘솔에 출력을 하였습니다.

![qa](http://localhost/content/images/2020/08/qa.png)

마블 다이어그램으로 이해하면 아래와 같습니다.

1. 메시지1(**파란공1**)이 **발행**
2. 메시지2(**파란공2**)이 **발행**
3. **발행 완료** (subscriber.complete())
4. 메시지3(**파란공3**)은 발행이 완료되었기 때문에 **발행이 안됨**.
5. subscribe가 **실행**
6. subscribe에서 발행완료 되기 전까지 발행된 메시지1(**빨간공1**)과 메시지2(**빨간공2**)를 출력하고, 발행이 완료되었을때(파이프) **발행완료**를 출력함.

```shell
# output

메시지1
메시지2
발행완료
```



### 2. Operators

**Operators**는 RxJS의 상당히 중요한 부분이며 종류도 쓰임새도 많습니다.  아래와 같은 카테고리가 있습니다.

1. Creation Operators
2. Join Creation Operators
3. Transformation Operators
4. Filtering Operators
5. Join Operators
6. Multicasting Operators
7. Error Handling Operators
8. Utility Operators
9. Conditional and Boolean Operators
10. Mathematical and Aggregate Operators

자세한 내용은 [RxJs 공식 도큐먼트](https://rxjs-dev.firebaseapp.com/guide/operators)를 참고해주시기 바랍니다.



**ajax**,**map**,**retry**를 이용하여 프리 api인  [Fake Online REST](https://jsonplaceholder.typicode.com/) 의 데이터를 가져와보겠습니다.

```javascript
import { map, retry } from 'rxjs/operators';
import { ajax } from 'rxjs/ajax';

let api_Observer = ajax('https://jsonplaceholder.typicode.com/users').pipe(
       map(e => e.response)
);
final_val.subscribe(x => console.log(x));
```

ajax()를 통해 api 객체 데이터를 **Observable**로 만들고 **pipe**를 이용하여 가져온 데이터들에 다른 연산(map)을 시켜줄 수 있습니다. **map**을 이용하여 가져온 api데이터의 response데이터를 **발행**하였습니다.

![ova](http://localhost/content/images/2020/08/ova.png)

마블 다이어그램으로 이해하면 아래와 같습니다.

1. ajax()를 통해 0부터 9(**파란공 0 - 9**)까지 **발행**
2. pipe를 이용하여 발행된 데이터들을 **map**처리를 함
3. subscribe가 **실행**
4. subscribe에서 map으로 처리된 발행된 메시지 0부터 9(**빨간공0 - 9**)를 출력함. 



```shell
# output

(10) […]
0: Object { id: 1, name: "Leanne Graham", username: "Bret", … }

... 중략

9: Object { id: 10, name: "Clementina DuBuque", username: "Moriah.Stanton", … }
```



### 3. Subject

**Subject** 클래스는 네 가지 종류가 있습니다. 

- Subject
- BehaviourSubject
- ReplaySubject
- AsyncSubject

#### Subject

**Subject**클래스는 subscribe가 실행된 이후에 발행된 모든 데이터를 출력됩니다.

```javascript
import { Subject } from 'rxjs';

const subject = new Subject();

subject.subscribe({
   next: (v) => console.log(`첫 번째 subscribe : ${v}`)
});
subject.next("빨간공");
subject.next("초록공");
subject.subscribe({
    next: (v) => console.log(`두 번째 subscribe : ${v}`)
 });
subject.next("파란공");
```

**Observable**에서는 처음에 next로 발행을 한 다음, subscribe를 통해 실행을 하는 구조였다면, **Subject**는 처음에 Subject를 통해서 subscribe를 합니다. 그 후 next를 이용하여 발행을 하고 실행이 되는 구조입니다.

![subject](http://reactivex.io/documentation/operators/images/S.PublishSubject.png)

[리액티브 프로그래밍 공식 홈페이지](http://reactivex.io/documentation/subject.html) 에서 가져온 마블 다이어그램으로 이해를 한다면,  

1. 첫 번째 subscribe가 **실행**
2. **빨간공**이 발행
3. **초록공**이 발행
4. 두 번 째 subscribe가 실행
5. **파란공**이 발행
6. 윗 줄(**첫 번째 subscribe**)에서는 **빨간공**, **녹색공**, **파란공**이 출력이되고, 아랫줄(**두 번째 subscribe**)에서는 **파란공**이 출력이 됨.



```shell
# output

첫 번째 subscribe : 빨간공
첫 번째 subscribe : 초록공
첫 번째 subscribe : 파란공
두 번째 subscribe : 파란공
```



#### BehaviorSubject

**BehaviorSubject**클래스는 subscribe가 실행된 **이전의 마지막 데이터**와 **이후에 발행된 모든 데이터**를 출력합니다.

```javascript
import { Subject, BehaviorSubject } from 'rxjs';

const behaviorSubject = new BehaviorSubject("보라공");

behaviorSubject.subscribe({
   next: (v) => console.log(`첫 번째 subscribe : ${v}`)
});
behaviorSubject.next("빨간공");
behaviorSubject.next("초록공");
behaviorSubject.subscribe({
    next: (v) => console.log(`두 번째 subscribe : ${v}`)
 });
behaviorSubject.next("파란공");
```

Subject와는 다른 점은 처음에 생성자안에 발행할 데이터를 설정이 가능합니다. 그리고 subscribe가 실행된 **이전의 마지막 데이터**도 출력이 된다는 것이 다른 점입니다.

![BehaviourSubject](http://reactivex.io/documentation/operators/images/S.BehaviorSubject.png)

[리액티브 프로그래밍 공식 홈페이지](http://reactivex.io/documentation/subject.html) 에서 가져온 마블 다이어그램으로 이해를 한다면,  

1. BehaviorSubscribe 생성자 안에 있는 **보라색 공**이 발행
2. 첫 번째 subscribe가 실행
3. **빨간공**이 발행
4. **초록공**이 발행
5. 두 번째 subscribe가 실행
6. **파란공**이 발행
7. 윗 줄(**첫 번째 subscribe**)에서는 **보라공**, **빨간공**, **녹색공**, **파란공**이 출력이되고, 아랫줄(**두 번째 subscribe**)에서는 **녹색공**, **파란공**이 출력이 됨.



```shell
# output

첫 번째 subscribe : 보라공
첫 번째 subscribe : 빨간공 
첫 번째 subscribe : 초록공
두 번째 subscribe : 초록공 
첫 번째 subscribe : 파란공 
두 번째 subscribe : 파란공
```



#### ReplaySubject

**ReplaySubject**클래스는  subscribe가 실행된 **처음 생성자에 설정한 수 만큼 이전의 마지막 데이터**를 출력하고, 위 Subject와 마찬가지로 **이후에 발행된 모든 데이터**를 출력합니다.

```javascript
import { ReplaySubject } from 'rxjs';

const replaySubject = new ReplaySubject(2);

replaySubject.subscribe({
   next: (v) => console.log(`첫 번째 subscribe : ${v}`)
});
replaySubject.next("빨간공");
replaySubject.next("초록공");
replaySubject.subscribe({
    next: (v) => console.log(`두 번째 subscribe : ${v}`)
 });
replaySubject.next("파란공");
```

ReplaySubject 생성자에 **숫자 2**를 설정하여, subscribe되기 이전 **두 개의 데이터**를 출력하는 것이 가능합니다.



![ReplaySubject](http://reactivex.io/documentation/operators/images/S.ReplaySubject.png)

[리액티브 프로그래밍 공식 홈페이지](http://reactivex.io/documentation/subject.html) 에서 가져온 마블 다이어그램으로 이해를 한다면,  

1. ReplaySubject 클래스 생성자에 `new ReplaySubject(2)` 처럼 **2**를 설정함.
2. 첫 번째 subscribe가 실행
3. **빨간공**이 발행
4. **초록공**이 발행
5. 두 번째 subscribe가 실행
6. **파란공**이 발행
7. 윗 줄(**첫 번째 subscribe**)에서는 **빨간공**, **녹색공**, **파란공**이 출력이되고, 아랫줄(두 번째 **subscribe**)에서는 생성자에 넣은 **2**만큼 **빨간공**,**녹색공**도 출력이되고 **파란공**도 출력이 됨.



```shell
# output

첫 번째 subscribe : 빨간공
첫 번째 subscribe : 초록공
두 번째 subscribe : 빨간공
두 번째 subscribe : 초록공
첫 번째 subscribe : 파란공
두 번째 subscribe : 파란공
```



#### AsyncSubject

**AsyncSubject**클래스는 마지막 발행 된 데이터만 출력을 합니다. 여기서 중요한 건, `complete()`로 **발행을 완료**를 시켜줘야 출력이 됩니다. 

```javascript
import { AsyncSubject } from 'rxjs';

const asyncSubject = new AsyncSubject();

asyncSubject.subscribe({
   next: (v) => console.log(`첫 번째 subscribe : ${v}`)
});
asyncSubject.next("빨간공");
asyncSubject.next("초록공");
asyncSubject.subscribe({
    next: (v) => console.log(`두 번째 subscribe : ${v}`)
});
asyncSubject.next("파란공");
asyncSubject.complete();
```

`asyncSubject.complete();` 를 마지막에 추가하여 **발행완료** 시켜주었습니다.



![AsyncSubject](http://reactivex.io/documentation/operators/images/S.AsyncSubject.png)

[리액티브 프로그래밍 공식 홈페이지](http://reactivex.io/documentation/subject.html) 에서 가져온 마블 다이어그램으로 이해를 한다면,  

1. 첫 번째 subscribe가 실행
2. **빨간공**이 발행
3. **초록공**이 발행
4. 두 번째 subscribe가 실행
5. **파란공**이 발행
6. 발행완료
7. 둘 다 **파란공**이 출력이 됨



```shell
# output

첫 번째 subscribe : 파란공
두 번째 subscribe : 파란공
```



### 4. Schedulers

**스케줄러**에서 세 가지의 **타입**이 있습니다.

- [asyncScheduler](https://rxjs-dev.firebaseapp.com/api/index/const/asyncScheduler)
- [asapScheduler](https://rxjs-dev.firebaseapp.com/api/index/const/asapScheduler)
- [queueScheduler](https://rxjs-dev.firebaseapp.com/api/index/const/queueScheduler)



#### asyncScheduler

말 그대로 **비동기처리 스케줄러**입니다.   

```javascript
import { asyncScheduler, asapScheduler, queueScheduler } from 'rxjs';

console.log('1!');
const task = () => console.log('2!');

asyncScheduler.schedule(task, 1000);

console.log('3!');
```

**비동기 처리**가 이루어지며, **2!**는 마지막에 출력 됩니다.

```shell
# output 

1! 
3! 
2!
```



#### asapScheduler

비동기처리중에서 **최대한 빠르게 작업을 처리** 하기 위한 스케줄러입니다.

```javascript
import { asyncScheduler, asapScheduler, queueScheduler } from 'rxjs';

console.log('1!');
asyncScheduler.schedule(() => console.log('async'));
asapScheduler.schedule(() => console.log('asap'));
console.log('2!');
```

**비동기 처리**이기 때문에 **1!**과 **2!**가 먼저 출력이 될 것이고, 순서상으로는 `async`가 위에 있기때문에 `async`가 먼저 출력이 될 것 같지만 asap은 최대한 비동기중에서 빠르게 처리되려고 하기때문에 `asap`이 먼저 실행됩니다. 하지만 `asap` 을 쓴다고 해서 항상 빠른 것은 아닙니다.

```shell
# output

1!
2!
asap 
async
```



#### queueScheduler

**delay가 있을 때**는 **asyncScheduler**와 똑같이 실행이 되고, **delay가 없을 때**는 **동기적**으로 스케줄링이 됩니다.

```javascript
import { asyncScheduler, asapScheduler, queueScheduler } from 'rxjs';

queueScheduler.schedule(() => {
    queueScheduler.schedule(() => console.log('queue1'));
    console.log('queue2');
  });
```

결과는 아래와 같습니다.

```shell
# output

queue2
queue1 
```



#### observeOn() VS subscribeOn()

위에서 스케줄러의 종류를 선택한 후에 `observeOn()`과 `subscribeOn()` 함수를 이용해서 스레드 제어가 가능합니다. `subscribeOn()` 함수는 **Observable**이 동작하는 스케줄러가 사용할 스레드를 지정하고, `observeOn()` 함수는 다음 **Observable**이 사용할 스레드를 지정합니다. 

![schedulers](http://reactivex.io/documentation/operators/images/schedulers.png)

[리액티브 프로그래밍 공식 홈페이지](http://reactivex.io/documentation/scheduler.html) 에서 가져온 마블 다이어그램으로 이해를 해봅시다.

1. 첫 번째 subscribe가 실행
2. `observeOn()`가 실행되면서 다음 Observable은 **주황색 스레드**에서 동작이 됨.
3. map이 실행되어 **원**을 **사각형**으로 바꾸었지만 map은 스레드와 관계없으므로 **주황색 스레드**로 동작이 됨.
4. subscribeOn()가 실행되어 가장 위에 있는 **시작하는 스케줄러**가 **파란색 스레드**로 적용이 됨. 하지만 진행에는 영향이 없음.
5. onserveOn()가 실행되면서 다음 Observable은 **보라색 스레드**로 동작이 됨.





## 참고

- [ReactiveX](http://reactivex.io)
- [tutorialspoint](https://www.tutorialspoint.com/rxjs)
- [RxJs](https://rxjs-dev.firebaseapp.com/)
- 유동환, 박정준, **RxJava 프로그래밍**, (한빛미디어, 2017)