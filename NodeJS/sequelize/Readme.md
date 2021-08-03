
# sequelize란 
---
nodeJS에서 mysql을 사용할 때 raw Query문을 사용하지 않고 더욱 쉽게 다룰 수 있도록 도와주는 라이브러리이다.\
sequelize는 ORM(Object-Relational Mapping)로 분류가 됩니다.\
ORM이란 객체와 관계형 데이터베이스의 관계를 매핑 해주는 도구이다.\
sequelize를 사용하면 raw Query문을 사용하지 않고 자바스크립트를 이용해서 mysql을 사용할 수 있다.


## 직접작성
- index.js 파일에서 반복문을 돌면서 models 폴더 내에 있는 파일들을 읽고, 그것들을 모델로 정의한다.\
그래서 models에 원하는 테이블 이름을 파일의 이름으로 js 파일로 만들어 준 후 모델을 정의 하면 테이블이 만들어진다.
- 모델을 정의하는 method는 define()이며, sequelize.define('객체 이름', 스키마 정의, 테이블 설정)로 사용할 수 있다.


## CLI 작성
models 폴더에 직접 작성하지 않고 터미널 창에서 명령어를 통해서도 테이블을 정의할 수 있다.
sequelize-cli란 sequelize command line interface의 줄임말이다.
즉 터미널에서 명령어를 사용해 데이터베이스 작업을 할 수 있게 만들어주는 툴이다.

cli로 모델을 만드는 기본 문법
```
sequelize model:create --name TABLE_NAME  --attributes "COLUMN1:type, COLUMN2:type, COLUMN3:type"
```
유저의 모델을 만드는 방법은
```
sequelize model:create --name user --attributes nickName: string, passWord: string
```
migrations 폴더에는 현재 시간을 이름으로 갖는 migration 파일이 생성된다.

## 주의 사항
- 같은 테이블 간 N:M 관계에서 모델 이름과 컬럼 이름을 따로 정해야 한다. (through 옵션 사용) 
### through옵션 
```
static associate(db) {
    db.User.hasMany(db.Post);
    db.User.belongsToMany(db.User, {
      foreignKey: 'followingId',
      as: 'Followers',
      through: 'Follow',
    });
    db.User.belongsToMany(db.User, {
      foreignKey: 'followerId',
      as: 'Followings',
      through: 'Follow',
    });
  }
```
