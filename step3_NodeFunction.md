# 노드 기능 알아보기

- 노드 모듈 시스템
- global 객체
- console 객체
- 타이머(setTimeout, setInterval, setImmediate)
- filename, dirname,process
- Module


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



## __filename, __dirname, process

- __filename

  - 현재 파일 이름
- __dirname

  - 현재 파일의 위치
- process
  - Node.js의 프로세스 정보를 제공
  - process.arch

    - 현재 사용중인 아키텍쳐
  - process.env

    - 환경변수 객체 반환
  - process.exit

    - Node.js의 프로세스를 종료
  - process.platform

    - Node.js 프로세스가 실행중인 운영체제 플랫폼 식별자를 반환
  - process.memoryUsage

    - Node.js 프로세스의 메모리 사용량을 바이트 단위로 설명하는 객체 반환
  - process.uptime

    - Node.js 프로세스가 실행된 시간 반환
  - process.versions

    - Node.js와 그 종속성 버전을 나열하는 객체 반환
  - process.cwd

    - 프로세스 실행 위치


## Module

- OS
  - 운영체제와 관련한 모듈
  - os.cpus
  - 그외, [API](https://nodejs.org/dist/latest-v11.x/docs/api/os.html)

- path
  - path와 관련한 모듈
  - 생각보다 좋은 기능이 많음, [API](https://nodejs.org/dist/latest-v11.x/docs/api/path.html)
    - path.join
      - 절대 경로 무시하고 경로의 이동을 해줌
    - path.resolve
      - 절대 경로 고려하고 경로의 이동을 해줌
  - **거의 모든 method와 property들이 사용되니 외워두면 좋음**

- url
  - url.Url
  - url.parse
  - 기존방식(`url.parse`)은 호스트가 없을 때도 사용가능, WHATWG방식(`url.Url`)은 search 처리가 편리
    - Search 처리 :  searchParams, queryString, url의 '?' 부분 이후  
- querystring
  - url.parse의 방식과 자주 쓰임,  WHATWG방식을 파싱 할때는 searchParams를 쓰이기 때문
  - querystring.parse()를 통해 파싱한 결과를 얻음
  - 반대로, searchParams의 `toString()`처럼 합치는 기능을 원할떈, `querystring.stringify()`를 사용함
- crypto
  - 다양한 방식의 암호화를 해주는 모듈
  - 단방향 암호화(해시)
    - hash방식으로 암호화해, 복호화를 하지 않는 방식,
    - 사용자의 비밀번호를 암호화해 저장한후 입력한 것과 비교한다. 사용자의 비밀번호를 가지고 있을 필요가 없음
    - 비밀번호 충돌 : 같은 비밀번호가 아니지만, 해쉬값이 같은 경우 (해커들이 자주 노린다, 짧을 수록 쉬움)
    - 그래서 해결방법(완벽은 아니지만) -> pbkdf2 암호화 방식
    - 실무에선 **bcrypt, scrypt**를 많이 쓴다. (추후 공부)
  - 양방향 암호화
    - 복호화가 가능한 방식
    - createCipher
      - Utf8 평문을 base64 암호문으로
    - createDecipher
      - base64암호문을 utf8 평문으로
    - 열쇠를 잘 숨겨서 복호화 되지 않게 유의
  - [API](https://nodejs.org/dist/latest-v11.x/docs/api/crypto.html), 공부해서 더 추가예정 API문서를 참고하자
- util
  - util.deprecate()
    - 곧 서비스 종료될 메서드임을 알려줄때
  - util.promisify()
    - callback hell을 막기위해 promise형식 쓸수 있도록 바꿔준다.
    - 모든 객체가 promise를 지원하지 않기때문에 쓰는것
    - prmoise형식으로 바꾸어 지면, async-await으로도 바꿀수 있다.
  - util.format()
    - console.log()와 비슷한 기능 단, 화면 출력이 아닌 문자열로 반환

- fs 모듈
  - 동기
    - `fs.readFile('경로','콜백')`,`fs.writeFile('경로','콜백')`
  - 비동기
    - `fs.readFileSync('경로')` 리턴값을 받고, 콜백을 쓰지 않음

- 버퍼와 스트림

  - 이미지를 업로드 할때 사용

  - 스트림은 이벤트 기반을 동작

  - 스트림은 버퍼의 흐름이기 때문에 여러개의 스트림을 이어 버퍼가 흘러가게 할 수 있음

    - 파이프를 이용해 잇는다. (복사한다)

    - 혹은 `fs.copyFile()`을 이용 할 수도 있다.

    - `createReadStream()`을 이용해 읽은 파일을 `zlib`로 압축하고 싶을때 파이프를 사용한다.

      ```javascript
      const zlibStream = zlib.createGzip();
      fs.createReadFile().pipe(zlibStream);
      ```

- 기타 fs 메서드

  - `fs.access()`
  - `fs.unlink()`
  - `fs.rmdir()`

- events 모듈

  - `.addListener()`
  - `.on()`
  - `.once()`
    - 한번만 불려지고 그 다음부터는 불려지지 않음
  - `.emit()`
    - 이벤트를 호출해줌
  - `.removeAllListeners()`
    - 하나의 이벤트에 여러 listener가 달리기 때문에 ALL 이다.
    - 하나만 지우겠다면 `.removeListener()`
  - `.listenerCount()`
    - 이벤트 리스너가 몇개 달려있는지

- 예외 처리하기

  - Node.js는 싱글스레드 이기 때문에 전체 서버가 죽게될 수 있으므로 예외처리가 필수
  - 예외가 발생하면 try ~ catch로 잡는다
    - 물론 try ~ catch 로 잡기전에 에러가 안잡을 수 있도록 만드는게 좋다.
  - 콜백으로 err가 잡힐땐 죽진 않는다. 다만 에러에 대한 처리가 필요
  - `process.on('uncaughtException',())`
    - Try ~ catch로 잡히는 에러를 한방에 잡아줌
    - 하지만, 여기에 의존하지 말고 근본적인 에러의 원인을 해결하는것이 중요
    - 가끔 콜백에 서버를 복구하는 코드를 넣는데, 무조건 실행된다는것을 보장하지 못한다.
