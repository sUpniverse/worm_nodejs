# Node.js 교과서 - 기본부터 프로젝트 실습까지

- START NODE
- ES2018









## START NODE

#### REPL과 헬로 노드

- Read, Evalute, Print, Loop
- console.log('hello node');



####호출 스택과 이벤트 루프

```javascript
/*
함수의 호출 third -> second -> first
*/
function first() {
    second();
    console.log('첫 번째');
}
function second() {
    third();
    console.log('두 번째');
}
function third() {
    console.log('세 번째');
}

first();
```

```javascript
/*
함수의 호출 시작 -> 끝 -> 3초후 실행 
*/
function run() {
    console.log('3초 후 실행');
}

console.log('시작');
setTimeout(run, 3000);
console.log('끝');
```

- 태스크 큐의 호출시기
  - setTimeout, setInterval, setImmediate, Promise resolve, reject, (async, await), 이벤트 리스너의 콜백



#### 이벤트 기반, 싱글쓰레드, 논블러킹IO

- 이벤트 기반(event driven)



## ES2018

#### const와 let

- const

  - 상수, 한번 선언하면 바꿀 수 없다.

  - 단, 객체를 넣고 객체안의 값은 변경가능

    ```javascript
    const h = [1,2,3,45];
    const h[0] = true;
    ```

- let

  - 변수, 값을 바꿀 수 있다.



####템플릿 문자열(백틱,`)

```javascript
const a = 'hello';
const b = true;
const c = 3;

//const d = a+' '+b+' '+c;
const d = `${a} ${b} ${c}`

//escaping 없이 따옴표도 가능
const d = `"'"''"`
```



#### 객체 리터럴의 변화

```javascript
/*
	예전의 문법
				*/
var sayNode = function(){
    console.log('Node');
};
var es = 'ES';
var oldObject = {
    sayJS: function(){
        console.log('JS')
    },
    sayNode: sayNode
};
oldObject[es+6] = 'Fantastic';

/*
	최근의 문법
				*/
var sayNode = function(){
    console.log('Node');
};
var es = 'ES';
var newObject = {
    sayJS(){
        console.log('JS');
    }
    sayNode,  
    [es+6]: 'Fantastic',
};
```



#### 화살표 함수 (=>)

- 기존의 function() {}와 거의 비슷 

  ```javascript
  function add1(x,y) {
      return x + y;
  }
  
  const add2 = (x,y) => {
      return x + y;
  }
  
  const add3 = (x,y) => x+y;
  ```

- 대체가 되지 못했던 점

  - this의 작동방식이 다름

    ```javascript
    var realationship1 = {
        name: 'zero',
        friends: ['nero','hero','xero'],
        logFriends: function() {
            var that = this;  //relationship1을 가리키는 this를 that에 저장
            this.friends.forEach(function(friend){
                console.log(that.name, friend);
            });
        },
    };
    
    const realationship1 = {
        name: 'zero',
        friends: ['nero','hero','xero'],
        logFriends() {
            var that = this;
            this.friends.forEach(friend =>{
                console.log(this.name, friend);
            });
        },
    };
    ```












