#START NODE

- REPL과 헬로 노드

- 호출 스택과 이벤트 루프

- 이벤트 기반, 싱글쓰레드, 논블러킹IO


##REPL과 헬로 노드

- Read, Evalute, Print, Loop

- console.log('hello node');


##호출 스택과 이벤트 루프

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



##이벤트 기반, 싱글쓰레드, 논블러킹IO

- 이벤트 기반(event driven)

