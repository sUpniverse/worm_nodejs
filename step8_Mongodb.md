# Mongo DB와  Mogoose 

- 설치 와 셋팅
  - 직접 mongo db를 설치할 수도 있지만,, 너무 지저분 하니

  - docker를 이용한다, [이용법](https://github.com/sUpniverse/cocoon_springboot/blob/master/Uses_Spring_Data.md#mongo-db), 스프링에서 썼지만 똑같으니 똑같이 쓴다.

    - spring과 다르게 nodejs로는 방법이 조금 달라서추가..

    - Mongo 커맨드 입력 후

      ```shell
      $ use admin
      $ db.createUser({ user: '이름', pwd: '비밀번호', roles: ['root'] })
      ```

    - 다시 exit후 mongo --auth

    - 그런후 mongo admin -u 이름 -p 비밀번호로 접속

- 특징과 컴퍼스

  - mongo db는 no sql로 서로간의 관계가 없다.
  - 구조는 db(schema) - collection(table) - documents(low)다
  - document의 컬럼은 마음대로 해도 된다. 다큐멘트마다 다를 수 있다.
  - 고정된 길이의 배열이면, 속성안에 넣고 or 계속 늘어날 것 같다면, 컬렉션을 별도로 둔다.
    - 아직 이말이 잘 이해되지 않는다.. 써보면서 이해하자
  - 몽구스는 몽고디비에 제약을 두지만 편의성과 안정성을 추가한다.
  - 자유도를 잘활용하면 고수가 될 수 있다. (단 개판으로 짜면,,책임이)

- 스키마 생성

  - sequelize랑은 조금 다르지만 비슷하다.

  - schemas라는 directory를 만들고 그안에 model처럼 정의한다.

    ```javascript
    /* index.js */
    const moongoose = require('mongoose');
    
    module.exports = () => {
        const connect = () => {
            moongoose.connect('mongodb://root:mongo_js@localhost:27017/admin', {
                dbName: 'mongo_js',
            },(error) =>{
                if(error) {
                    console.log('몽고디비 연결 에러', error);
                } else {
                    console.log('몽고디비 연결 성공');
                }
            });
        }
        connect();
    
        mongoose.connection.on('error', (error) => {
            console.error('몽고디비 연결 에러', error);
        });
        mongoose.connection.on('disconnected', (error) => {
            console.error('몽고디비 연결이 끊어졌습니다. 연결을 재시도 합니다.');
            connect();
        });
    
        require('./user');
        require('./comment');
    }
    ```

    - 몽구스와 연결 및 user, comment model을 연결해둔다.

    ```javascript
    /* user.js */
    const mongoose = require('mongoose');
    
    const { Schema } = mongoose;
    const userSchema = new Schema({
        name: {
            type: String,
            required: true,
            unique: true,
        },
        age: {
            type: Number,
            required: true,
        },
        married: {
            type: Boolean,
            required: true
        },
        comment: {
            type: String
        },
        created_at: {
            type: Date,
            default: Date.now
        }
    })
    
    module.exports = mongoose.model('User',userSchema);
    ```

    ```javascript
    /* comment.js */
    const mongoose = require('mongoose');
    
    const { Schema } = mongoose;
    const commentSchema = new Schema({
        commenter: {
            type: ObjectId,
            required: true,
            ref: 'User'
        },
        comment: {
            type: String,
            required: true
        },
        created_at: {
            type: Date,
            default: Date.now
        }
    
    })
    
    module.exports = mongoose.model('Comment',commentSchema);
    ```

  - index, user, comment의 구조를 거의 외우다 시피 해야겠찌?

  - app.js에 sequelize때 처럼, schemas를 연결해주고 실행한다.

- sequelize의 include처럼 관계 설정하기 

  - .populate()를 해주면된다.
  - include는 join이지만, populate는 mongodb내에 있는 기능이 아닌, mongoose가 하는 기능이다.
  - 그래서 성능이 좀 떨어진다.