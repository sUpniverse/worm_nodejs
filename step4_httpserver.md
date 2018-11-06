# http 모듈로 웹 서버 만들기

- http,https,http2
- 쿠키, 세션
- cluster를 이용한 멀티 프로세싱



## http, https, http2

- http 모듈, localhost, 포트

  - `http.createServer((req,res)).listen('port')`
  - 기본 포트 : 80

- 응답으로 파일 읽어 보내기

  ```javascript
  const server = http.createServer((req,res) => {
      console.log('서버 실행');
      fs.readFile('./server2.html',(err,data)=> {
          if(err){
              throw err;
          }
          res.end(data);
      });
  }).listen(8081);
  ```

- https, http2

  - https 기본 포트 : 443 

  ```javascript
  https.createServer({
      cert: fs.readFileSync('도메인 인증서 경로');
      key: fs.readFileSync('도메인 비밀키 경로');
      ca: [
  		fs.readFileSync('상위 인증서 경로'),
          fs.readFileSync('상위 인증서 경로'),
          fs.readFileSync('상위 인증서 경로'),
          fs.readFileSync('상위 인증서 경로'),
      ],
  },(req,res) => {
      res.end('https server');
  }).listen(443);
  ```

  - https는 암호화이기 때문에 인증서가 필요하다.
  - http2
    - https 기반으로 동작하므로 인증서가 필요
    - 익스프레스랑 호환 문제가 있다 (spdy를 대신 사용)





## 쿠키, 세션

- 쿠키 설정하기, req.url

  ```javascript
  const server = http.createServer((req,res) => {
  	req.headers.cookie
      res.writeHead(200,{'Set-Cookie':'mycookie=test'});
      res.writeHead(200,{'키':'값'})    
  }).listen(8081);
  ```

- 쿠키와 세션

  - 쿠키

    - maxAge : 쿠키의 만료 시간을 밀리초 단위로 설정

    - expires : 쿠키의 만료 시간을 표준 시간 으로 설정

    - path : 쿠키의 경로 (default: /)

    - domain : 쿠키의 도메인 이름 (default: loaded)

    - secure : HTTPS 프로토콜만 쿠키 사용 가능

    - httpOnly : HTTP 프로토콜만 쿠키 사용 가능

    - signed : 쿠키의 서명 여부를 결정

      ```javascript
      res.writeHead(200,{'Set-Cookie':`name=${encodeURIComponent(name)}; Expire=${expires.toGMTString}; HttpOnly; path=/`});
      
      /* 혹은 이렇게,, 대신 cookie-parser 필요 */
      res.cookie(‘favorite’, favorite, {
        maxAge: 30000   // 30000밀리초 → 30초
      });
      ```

  - 세션

    - secret : 세션을 암호화 해줌
    - resave : 세션을 항상 저장할지 여부를 정하는 값. (false 권장)
    - saveUninitialized : 초기화되지 않은채 스토어에 저장되는 세션
    - store : 데이터를 저장되는 형식



## cluster를 이용한 멀티 프로세싱

- Node는 싱글스레드 이므로 코어를 1개밖에 사용하지 않는다.
- 서버는 작아도 4개정도의 코어를 가지고 있기때문에, 비효율적인 동작이 된다.
- 효율적인 사용을 위해 clustering을 통해 놀고있는 코어를 활용한다.
- cluster
  - master
  - worker