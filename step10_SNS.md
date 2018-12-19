#  SNS 서비스





## dotenv

- .env 파일에 config에 관련된 모든 내용을 작성함

- 그런다음 해당 원하는 곳에서 require('dotenv').config()

- 그리고 원하는 키를 가져다 쓰고 싶은곳에 process.env.변수명 을 가져다가 쓰면 된다.





## passport

- Passort, passport-local, pass-kakao 등 원하는 로그인 기능 가진것을 npm을 통해 받는다

- bcrypt도 같이 다운 받는다 (암호화 관련)

- passport의 미들웨어도 이용한다 (app.js에 등록해줘야한다.)

  - passport.initialize()
  - passport.session()

- local의 전략(회원을 어떻게 인증할지, email등으로 할때)

  - passport-local을 이용해 재정의 한다.
  - 아이디 체크 - 비밀번호 체크 전략등을 정의한다.

  ```javascript
  const LocalStrategy = require('passport-local').Strategy;
  const bcrypt = require('bcrypt');
  
  module.exports = (passport) => {
      passport.use(new LocalStrategy({
          usernameField: 'email',
          passwordField: 'password',
      }, async (email, password, done) => {
          try {
              const exUser = await User.find({where: {email}});
              if(exUser) {
                  const result = await bcrypt.compare(password, exUser.password)
                  if(result) {
                      done(null, exUser);
                  } else {
                      done(null, false, {message: '비밀번호가 일치하지 않습니다.'});
                  }
              } else {
                  done(null, false, {message: '가입되지 않은 회원입니다.'});
              }
  
          }catch (error) {
              console.error(error);
              done(error);
          }
      }));
  };
  ```

  - done(에러, 성공, 실패) => (authErr, user, info)로 전달된다. (router 부분에)

- Router에서 기능

  - 회원가입
  - 로그인
  - 로그아웃

- serializeUser / deserializeUser

  - serializeUser : 로그인 성공 시, 설정 해놓은 대로 세션에 저장
  - deserializeUser : 세션의 정보 (req.user)를 이곳에서 받아옴

  ```javascript
  passport.serializeUser((user, done) => {
          done(null, user.id);
      });
  
      passport.deserializeUser((id, done) => {
          if(user[id]) {
           done(user[id]);
          } else {
              User.find({ where: { id }})
                  .then(user => user[id] = user, done(null, user))
                  .catch(err => done(err));
          }
      });
  ```

  - done()에서 user.id를 받아서 세션에 저장
  - User.id로 저장했기 때문에, deserializeUser에서 id 정보로 받아옴
  - User.find가 계속 불려지면 DB를 계속 조회해야 하기 때문에 캐싱을 이용해 저장


