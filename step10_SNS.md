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
      /*  if(user[id]) {
           done(user[id]);
          } else {              */
          User.find({ where: { id }})
     //       .then(user => user[id] = user, done(null, user))
              .then(user => done(null, user))
              .catch(err => done(err));
     //   }
  });
  ```

  - done()에서 user.id를 받아서 세션에 저장
  - User.id로 저장했기 때문에, deserializeUser에서 id 정보로 받아옴
  - User.find가 계속 불려지면 DB를 계속 조회해야 하기 때문에 캐싱을 이용해 저장
    - but 강의에서는 후에 삭제함 주석 처리해논 이유가 그것.. (빈 객체가 불려져서..)

- 소셜 로그인 (passport-kakao)

  - 소셜 페이지에 자신의 앱을 등록하고 발급된 Id를 .env에 저장해준다.
    - ID와 callback url을 등록해준다.
  - 받아온 아이디 정보를 일단 대조해 보고 있으면, 반환하고 없으면 회원등록을 한다.
  - 각각 소셜마다 반환정보 및 json 형태가 다르기때문에, 확인해 봐야한다.

  ```javascript
  const KakaoStrategy = require('passport-kakao').Strategy;
  const { User } = require('../models');
  
  module.exports = (passport) => {
      passport.use(new KakaoStrategy({
          clientID: process.env.KAKAO_ID,
          callbackURL: '/auth/kakao/callback',
      }, async (accessToken, refreshToekn, profile, done) => {
          try {
              const exUser = await User.find({
                  where: {
                      snsId: profile.id,
                      provider: 'kakao'
                  }
              });
              if(exUser) {
                  done(null, exUser);
              } else {
                  const newUser = await User.create({
                      email: profile._json && profile._json.kaccount_email,
                      nick: profile.displayName,
                      snsId: profile.id,
                      provider: 'kakao'
                  });
                  done(null, newUser);
              }
          } catch (error) {
              console.log(error);
              done(error);
          }
      }));
  };
  ```



## 이미지 업로드 (multer)

- 서버 스토리지에 이미지를 저장 or S3, Google cloude 같은곳에 저장하는 법

- 설정

  ```javascript
  const upload = multer({
      //서버의 저장공간을 지정
      storage: multer.diskStorage({
          destination(req, file, cb) {
              cb(null, 'uploads/');
          },
  		//파일 설정
          filename(req, file, cb) {
              //파일 확장자 가져오기
              const ext = path.extname(file.originalname);
              //파일의 이름을 무엇으로할지 (파일이름 + 날짜시간 + 파일확장자)
              cb(null, path.basename(file.originalname, ext) + new Date().valueOf() + ext);
          }
      }),
      // 파일 사이즈 제한
      limit: { fileSize: 5 * 1024 * 1024},
  });
  ```

- 라우팅

  ```javascript
  // single : 이미지하나 , array: 이미지 여러개
  router.post('/img',upload.single('img'), (req, res) => {
      console.log(req.file);
      res.json({url: `/img/${req.file.filename}`})
  });
  ```

  - single : 이미지 하나
  - array : 이미지 여러개 (단일 필드)
  - Field : 이미지 여러개 (여러 필드)
  - None : 이미지 X

## 해시태그

- '#' 으로 데이터 표현, 각 주제를 DB에 등록해서 찾는다.

- 해시태그 가져오기

  ```javascript
  // #으로 된 단어들을 먼저 가져온다. 정규표현식 사용
  const hashtags = req.body.content.match(/#[^\s]*/g);
          if (hashtags) {
              const result = await Promise.all(hashtags.map(tag => Hashtag.findOrCreate({       //findOrCreate : DB에 있으면 찾고 없으면 새로 생성(중복체크)
                  where: {title: tag.slice(1).toLowerCase()}  // 1번째 문자인 #제거
              })));
              await post.addHashtags(result.map(r => r[0]));
          }
  ```

- 해시태그 검색

  ```javascript
  router.get('/hashtag', async (req, res, next) => {
      //검색된 해시태그 가져오기
      const query = req.query.hashtag;
      if(!query) {
          return res.redirect('/');
      }
      try {
          //검색된 해시태그 db에서 찾기
          const hashtag = await Hashtag.find({where: {title: query}});
          let posts =[];
          if(hashtag) {
              // 해시태그가 있으면, 해당되는 post들을 가져오기
              posts = await hashtag.getPosts({ include: [{ model: User}]});
          }
          // 게시글 뿌려주기
          return res.render('main', {
              title: `${query} | NordBird`,
              user: req.user,
              twits: posts
          });
      }catch (error) {
          console.error(error);
          next(error);
      }
  })
  ```




## Sequelize 관계 설정 

- User <-> Post

  ```javascript
  db.User.hasMany(db.Post);
  db.Post.belongsTo(db.User);
  ```

- Post <-> Hashtag		

  ```javascript
  db.Post.belongsToMany(db.Hashtag, { through: 'PostHashtag'});
  db.Hashtag.belongsToMany(db.Post, {through: 'PostHashtag'});
  ```

- User <-> User

  ```javascript
  db.User.belongsToMany(db.User,{through: 'Follow', as:'Followers', foreignKey:'followingId'})
  db.User.belongsToMany(db.User,{through: 'Follow', as:'Followings', foreignKey:'followerId'})
  ```

  - 같을 땐, as를 통해 구별한다
  - 외래키를 이용해서 서로의 값을 지정

- User <-> Post (like)

  ```javascript
  db.User.belongsToMany(db.Post, {through: 'Like'});
  db.Post.belongsToMany(db.User, {through: 'Like', as: 'Liker'});
  ```

- A.getB : 관계있는 로우 조회
- A.addB : 관계 생성
- A.setB : 관계 수정
- A.removeB : 관계 제거



## 보안

- uuid
- JWT
- jsonwebtoken
  - 토큰을 만들어 주는 패키지



## CORS 해결하기

- 프론트에서 다른 오리진의 서버로 요청을 보내면 발생하는 에러
- 서버에서 서버는 CORS 에러가 나지 않는다.
- Access-Control-Allow-Origin 헤더를 응답 헤더에 넣어주면 문제가 해결된다.
- `npm cors`을 통해서 해결하면된다.
- 쉽게 하면, cors 미들웨어를 통해 헤더에 Access-Control-Allow-Origin을 넣어주어 해결하는 형태이다.