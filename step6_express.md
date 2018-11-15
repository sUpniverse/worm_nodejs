# 익스프레스 웹 서버

- express start
- Middle
- Template engine



## Express-generator, npm scripts, bin/www

- express를 이용한, 자동 생성

  ```shell
  $ npm install -g express-generator 
  ```

  - express 명령어를 쓰기 위한 설치

- 앱 생성

  ```shell
  $ express <appname> view=pug
  ```

- 그럼 다음과 같이 뜨는데 똑같이 따라 치면된다

  ```shell
  #change directory:
  $ cd learn-express
  
  #install dependencies:
  $ npm install
  
  #run the app:
  $ DEBUG=learn-express:* npm start
  ```

- `./bin/www`가 핵심



## MiddleWare

- app.use()를 이용해 미들웨어를 연결 (app.get(), app.post()등 특수 목적에 따른 연결도 있음 )

- next()를 이용해 다음 미들웨어를 호출

- 유명한 미들웨어

  - logger

    - 요청과 응답에 대한 log 반환

  - morgan

  - express-session

    - 메모리 세션 활성화

    - 외워둘 예제 (step9에서 자세히 설명)

      ```javascript
      app.use(cookieParser('secret code'));
      session = {};
      app.use(session({
          resave: false,	// 세션 객체 수정사항이 없어도 저장할지?
          saveUninitalized: false, // 처음의 빈 세션 객체라도 저장 할지?
          secret: 'secret code',
          cookie: {
              httpOnly: true,
              secure: false,		//https 쓸건지?
          },
      }));
      ```

  - connect-flash

    - 로그인 실패했을때 pop up 메세지, 1회성 메세지를 표시해줌

  - body-parser

    - request body  파싱할때 쓴다.
    - express 대체

  - cookie-parser

    - 쿠키를 파싱해준다.

  - express.static()

    - 정적파일 불러올때 쓴다.
    - next()를 하지 않고, 원하는 파일이 없을때만 next()를 하기때문에 맨처음에 app.use에 넣는다

- 라우팅 미들웨어(라우터도 미들웨어다!)

  - app.get, app.post등이 많아지면,, 서비스가 커지면 코딩이 많아지니 분리한다.

    ```javascript
    /* app.js */
    app.use("/",indexRouter)
    
    /* indexRouter */
    var router = express.Router();
    router.get('/posts');
    module.exports = router;
    ```

- 404 처리 미들웨어와 에러 처리 미들웨어

  - controller(app.js)가 주소를 찾는데 없으면 404를 띄워야 한다.

  - next도 하지 않고, res 메서드도 사용하지 않으면 클라이언트는 계속 기다린다. (실제로는 timeout될때까지)

  - 404 처리, express에서는 writeHead() 대신 status()를 쓴다.

    ```javascript
    app.use((req,res, next)=> {
        res.status(404).send('NOT FOUND');
    });
    ```

  - 에러 처리

    ```javascript
    app.use(function(err, req, res, next) {
        console.log(err);
        res.status(500).send('SERVER ERROR');
    });
    ```

    - 에러 처리할때는 파라미터에 err를 넣어야한다.

  - 404, 에러에 관한 미들웨어는 맨 마지막단에 둔다.

- 꿀팁들

  - res.locals를 통해 변수 정의를 할 수 있다.
  - req.app.get('')을 통해 express에 변수 정의를 할 수도 있다. (전역 변수처럼 쓸 수 있다.)
    - 그게 위험하면,, req.'변수'를 통해 임시로 정의 할 수도 있다. 
  - 미들웨어앞에 다른 기능들을 넣고 싶을때 => step.9에서 
  - res.json을 통해 json 데이터를 보낼수도 있음


## Template Engine 

- Pug 기본문법

  - pug 설정

    ```javascript
    app.set();
    app.set();
    ```

  - pug는 들여쓰기로 부모 자식 태그를 구분, html처럼 <>를 쓰지 않는다.

  - 들여쓰기는 탭이든 스페이스든 상관없지만 둘중 하나로 통일해야한다.

  - 속성은() 안에, div는 생략가능, 내용은 태그 한 칸 띄고 작성

  - 아이디는 #, 클래스는 . , |로 여러 줄, 태그.으로 여러줄

- Pug 심화

  - if, else if, else 등을 쓸수 있다, unless도 가능함
  - for 아이템, 인덱스 in (for each)등도 사용가능
  - include 기능도 가능, 또는 layout을 이용하여 중복되는 부분 사용
    - 부분 부분은 include로
    - 전체 부분은 layout로 해결 (block을 정의해서 사용)

- EJS

  - JSP 문법과 비슷 <% %>을 이용해서쓴다.
  - include기능 가능, layout은 안됨

- nunchucks

  - EJS를 쓸바엔 요걸 쓴다.