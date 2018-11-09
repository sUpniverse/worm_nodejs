# 익스프레스 웹 서버

- Express-generator, npm scripts, bin/www



## Express-generator, npm scripts, bin/www

- express를 이용한, 자동 생성

  ```
  $ npm install -g express-generator 
  ```

  - express 명령어를 쓰기 위한 설치

- 앱 생성

  ```
  $ express <appname> view=pug
  ```

- 그럼 다음과 같이 뜨는데 똑같이 따라 치면된다

  ```
  change directory:
  $ cd learn-express
  
  install dependencies:
  $ npm install
  
  run the app:
  $ DEBUG=learn-express:* npm start
  ```

- `./bin/www`가 핵심

