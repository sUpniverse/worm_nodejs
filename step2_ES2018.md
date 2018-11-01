# ES2018

- const와 let
- 템플릿 문자열(백틱,`)

- 객체 리터럴의 변화

- 화살표 함수 (=>)

- 비구조화 할당(destructuring)

- rest 문법과 Q&A

- 콜백과 프로미스(Promise) 

- async/await


## const와 let

- const

  - 상수, 한번 선언하면 바꿀 수 없다.

  - 단, 객체를 넣고 객체안의 값은 변경가능

    ```javascript
    const h = [1,2,3,45];
    const h[0] = true;
    ```

- let

  - 변수, 값을 바꿀 수 있다.


## 템플릿 문자열(백틱,`)

```javascript
const a = 'hello';
const b = true;
const c = 3;

//const d = a+' '+b+' '+c;
const d = `${a} ${b} ${c}`

//escaping 없이 따옴표도 가능
const d = `"'"''"`
```



## 객체 리터럴의 변화

```javascript
/*
	예전의 문법
				*/
var sayNode = function(){
    console.log('Node');
};
var es = 'ES';
var oldObject = {
    sayJS: function(){
        console.log('JS')
    },
    sayNode: sayNode
};
oldObject[es+6] = 'Fantastic';

/*
	최근의 문법
				*/
var sayNode = function(){
    console.log('Node');
};
var es = 'ES';
var newObject = {
    sayJS(){
        console.log('JS');
    }
    sayNode,  
    [es+6]: 'Fantastic',
};
```



## 화살표 함수 (=>)

- 기존의 function() {}와 거의 비슷 

  ```javascript
  function add1(x,y) {
      return x + y;
  }
  
  const add2 = (x,y) => {
      return x + y;
  }
  
  const add3 = (x,y) => x+y;
  ```

- 대체가 되지 못했던 점

  - this의 작동방식이 다름

    ```javascript
    var realationship1 = {
        name: 'zero',
        friends: ['nero','hero','xero'],
        logFriends: function() {
            var that = this;  //relationship1을 가리키는 this를 that에 저장
            this.friends.forEach(function(friend){
                console.log(that.name, friend);
            });
        },
    };
    
    const realationship1 = {
        name: 'zero',
        friends: ['nero','hero','xero'],
        logFriends() {        
            this.friends.forEach(friend =>{
                console.log(this.name, friend);
            });
        },
    };
    ```


## 비구조화 할당(destructuring)

```javascript
//step.1
const status = candyMachine.status;
const getCandy = candyMachine.getCandy;
const a = candyMachine.a;
const b = candyMachine.b
/* 요렇게 변화 할 수 있음, 많으면 많을 수록 효율적 */
const [status, getCandy,a,b] = candyMachine;

//step.2
var array = ['nodejs',{},10,true];
var node = array[0];
var obj = array[1];
var bool = array[array.length - 1];

/* 위와 같다.  */
const array = ['nodejs',{},10,true];
const [node,obj, ,bool] = array;
    
```

- 그러나 비구조화 할당시에 this가 의도와 다르게 동작할 수도 있다.

  ```javascript
  const candyMachine = {
      status: {
          name: 'node',
          count: 5,
      },
      getCandy() {
          this.status.count--;
          return this.status.count;
      }
  };
  //
  const [getCandy] = candyMachine;
  getCandy();
  ```

  - getCandy를 하면 값을 불러오지 못한다.


## rest 문법과 Q&A

- …변수

  - rest로 여러개의 변수를 모아서 배열로 만든다.

    ```javascript
    const p = (...y) => console.log(y)
    p(5,6,7,8,9)
    // [5,6,7,8,9]가 출력된다.
    ```



#### 콜백과 프로미스(Promise) 

- 비동기로 실행되는 콜백의 단점을 보완하려고 프로미스를 도입

- 실행순서가 어떻게 흘러가는지 파악하기 어려운 단점 (괄호가 늘어난다)

  ```javascript
  Users.findOne('zero',(err,user)=> {
      if(error){
          return console.log(err);
      }
      console.log(user);
      Users.update('zero','nero',(err, updateUser) => {
          if(error){
          	return console.log(err);
     		}
          console.log(updateUser);
          Users.remove('nero', (err,removeUser) => {
              if(error){
          	return console.log(err);
     			}
              console.log(removeUser);
          });
      });
  });
  /*
  	콜백을 호출하면서 callback hell이 계속해서 만들어짐  
  	변수에 콜백을 담아서 줄여왔었음..
  */    
  
  Users.findOne('zero')
      .then((user) => {
   		console.log(user);
      	return Users.update('zero','nero');
  	})
      .then((updatedUser) => {
              
  	})
      .then((removeUser) => {
      
  	})
      .catch((error) => {
          console.error(error);
      })
  ```

- 프로미스 (객체로 new를 이용해 재정의 가능)

  ```javascript
  /* step.1 */
  new Promise((resolve, reject) => {
      const a = 1;
      const b = 2;
      if(a + b > 2) {
          resolve(a + b);
      } else {
          reject(a - b);
      }
  });
  
  plus
      .then((success) => {
      	console.log(success);
  	})
      .catch((fail) => {
      	console.lg(fail);
  	})
  
  
  /* step.2 */
  promise
      .then((message) => {
      	return new Promise((resolve, reject)=> {
              resolve(message2);
      	});
  	})
  	.then((message2) => {
      	console.log(message2);
          return new Promise((resolve, reject) => {
              reject(message3);
          });
  	})
      .then((message3) => {
          console.log(message3);
      })
      .catch((error) => {
      	console.error(error);
      });
  ```

- 프로미스 API

  ```javascript
  Promise.all([Users.findOne(), Users.update, Users.remove()])
  	.then((result) => {})
  	.then((error) => {})
  ```

  - 3개를 동시에 담아서 사용할 수도 있다. (단, 하나라도 error가 나면 error로 진행)


##async/await

- Promise의 then(), catch() 를  사용하지 않고 동기식으로 보여지게 도와준다.

```javascript
async func() => {
    try {
        const user = await Users.findOne('zero');
        console.log(user);
        const updatedUser = await Users.update('zero','nero');
        console.log(updatedUser);
        const removedUser = await Users.remove('nero');
        console.log(removedUser);
        console.log('다 찾았니?');
    } catch (err) {
        console.error(err);
    }
}
func()
```

- await 사용을 위해선 async를 꼭 선언해 줘야함
- call -> prmoise -> async,await 로 비교해보면 간편함을 볼 수 있다.





