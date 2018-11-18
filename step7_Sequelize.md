## MySQL과 시퀄라이즈

- MySQL을 편리하게 사용하기위해, ORM인 시퀄라이즈를 이용한다.

- 설치

  - npm install sequelize mysql2
  - npm i -g sequelize-cli
    - sequlize를 쓸때는 command line interface를 같이 설치해 준다.

- 모델 정의 

  ```javascript
  /* model/user.js */
  module.exports = (sequelize, DataTypes) => {
      return sequelize.define('user', {
          name: {
              type: DataType.STRING(100)
          }
      })
  }
  
  /* model/comment.js */
  module.exports = (sequelize, DataTypes) => {
      return sequelize.define('comment',{
          comment: {
              type:DataType.STRING(100)
          }
      })
  }
  
  /* model/index.js */
  db.User = require('./user')(sequelize, Sequelize);
  db.Comment = require('./comment')(sequelize, Sequelize);
  ```

- 관계설정 (hasMany, belongsTo)

  ```javascript
  /* model/index.js */
  db.User.hasMany(db.Comment, {foreignKey: 'commenter', sourceKey: 'id'});
  db.Comment.belongsTo(db.User, {foreignKey: 'commenter', targetKey: 'id'});
  ```

- 디비 스키마 생성

  ```shell
  $ sequelize db:create
  ```

- 테이블 생성

  ```javascript
  /* app.js */
  var { sequelize } = require(./models);
  sequelize.sync();
  ```

  - 비구조화 할당(destructuring)을 통해 sequelize 객체를 할당받고 `.sync()`를 통해 db table을 생성한다.

- 라우터 만들기

  - routes 폴더 하위에 만들었던, 모델들에 관한 router를 만들어 준다 

  - controller 개념?, 근데 app.js에도 전체 등록해야함

  - C

    ```javascript
    router.post('/',(req,res) => {
        Comment.create({
            commenter: req.body.id,
            comment: req.body.comment
        })
            .then((result) => {
                console.log(result);
                res.status(201).json(result);
            })
            .catch((err) => {
                console.error(err);
                next(err);
            })
    });
    ```

  - R

    ```javascript
    router.get('/:id',(req,res) => {
        Comment.findAll({
            include: {
                model: User,
                where: {id: req.params.userId}
            }
        })
            .then((comments) => {
                console.log(comments);
                res.json(comments);
            })
            .catch((err) => {
                console.error(err);
                next(err);
            })
    });
    ```

    - 특별한 case로 userId를 이용해서 게시물 전체를 긁어오는 R임
    - join된 User의 id를 통해 게시물을 가져오는것으로, include 기능이 추가됌

  - U

    ```javascript
    router.patch('/:id',(req,res) => {
        Comment.update({
            comment: req.body.comment
        },{
            where: {id: req.params.id}
        })
            .then((result) => {
                console.log(result);
    
            })
            .catch((err) => {
                console.error(err);
                next(err);
            });
    
    });
    ```

  - D

    ```javascript
    router.delete('/:id',(req,res) => {
        Comment.destroy({
            where: {id: req.params.id}
        })
            .then((result) => {
                console.log(result);
                res.status(201).json(result);
            })
            .catch((err) => {
                console.error(err);
                next(err);
            });
    });
    ```
