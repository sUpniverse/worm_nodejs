# 노드 기능 알아보기

- 노드 모듈 시스템

- global 객체

- console 객체

- 타이머(setTimeout, setInterval, setImmediate)


## 노드 모듈 시스템

- Module

  - module 객체로 각 모듈에 지역적이며, 비공개(private)

- Module.exports

  - 객체 참조로 require() 호출을 하면 받는 값
  - 빈 객체이다 (default)

- exports

  - 편의(편리) 변수 (convenience variable)
  - 코딩을 덜하기 위해 도와주는 변수
  - 그저 참조일뿐, 반환하지 않는다.
  - module.exports를 참조하는 변수이다.

- Module.exports = {odd, even} 

  - exports.odd = odd

  - exports.even = even 


## global 객체

- 노드의 전역객체 global
  - 전역 객체이기 때문에 파일 간 global 공유가능



## Console 객체

- 웹 브라우저에서 제공하는 JavaScript 콘솔 메커니즘과 유사한 표준 출력(stdout)과 표준 오류(stderr)를 표시하는 디버깅 콘솔을 제공, [API](https://nodejs.org/dist/latest-v8.x/docs/api/console.html)
- 주요 API
  - console.time()
    - 시간측정, 위에 선언후 뒤에 console.timeEnd를 해주면 그사이의 시간을 측정
  - console.log()
  - console.error()
    - console.trace(), 에러안에서 에러 위치를 추적해준다.
  - console.dir()
    - 객체 전용 log, 객체 logging할때 편함



## 타이머

- setTimeout()
  - 시간지연 뒤 콜백함수 호출  
  - clearTimeout()으로 시간지연 해제 가능
- setInterval()
  - 지연 시간 마다 콜백함수 반복 실행
  -  마찬가지로 clearInterval()로 해제 가능
- setImmediate()
  - 콜백을 즉시 실행
  - 이벤트 루프로 보내고 싶을때 setImmediate()를 사용
  - clearImmediate()로 즉시 실행할 콜백 취소

