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