# 1.1 Design Patterns

- [1.1.1 싱글톤 패턴](#1.1.1-싱글톤-패턴)
  - [싱글톤 패턴 예시](#데이터베이스-연결-모듈)
  - [싱글톤 패턴의 단점](#싱글톤-패턴의-단점)
  - [의존성 주입](#의존성-주입)
  
</br>

---

# 1.1.1 싱글톤 패턴
## 디자인 패턴이란?
프로그램을 설계할 때 발생했던 문제점들을 해결할 수 있도록 객체간의 상호관계를 규약 형태로 만들어놓은 것
- 플레이코드 링크 : https://playcode.io/new/
- 코딩그라운드 링크 : https://www.tutorialspoint.com/compile_java_online.php

## 싱글톤 패턴(Singleton pattern)
- 하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴
- 하나의 인스턴스를 기반으로 로직을 만드는데 쓰이며, 데이터베이스 연결 모듈에 많이 사용
- 장점 : 인스턴스 하나를 다른 모듈들이 공유하면서 사용하기 때문에 인스턴스 생성 비용이 줄어든다.
- 단점 : 의존성이 높아진다.

### 데이터베이스 연결 모듈
```javascript
// DB 연결을 하는 것이기 때문에 비용이 더 높은 작업 - from 원본 github 코드
const URL = 'mongodb://localhost:27017/kundolapp' 
const createConnection = url => ({"url" : url})    
class DB {
    constructor(url) {
        if (!DB.instance) { 
            DB.instance = createConnection(url)
        }
        return DB.instance
    }
    connect() {
        return this.instance
    }
}
const a = new DB(URL)
const b = new DB(URL) 
console.log(a === b) // true 싱글톤이 아닌상태에서 그냥 new 하면 === 결과가 false로 나온다.
```

### 자바에서의 싱글톤 패턴
자바로는 중첩 클래스를 이용하는 방법이 가장 대중적이라고 한다. (위의 자바스크립트 예시와 거의 동일)
```java
class Singleton {
    private static class singleInstanceHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getInstance() {
        return singleInstanceHolder.INSTANCE;
    }
}

public class HelloWorld{ 
     public static void main(String []args){ 
        Singleton a = Singleton.getInstance(); 
        Singleton b = Singleton.getInstance(); 
        System.out.println(a.hashCode());
        System.out.println(b.hashCode());  
        if (a == b){
         System.out.println(true); 
        } 
     }
}
/*
705927765
705927765
true

1. 클래스안에 클래스(Holder),
static이며 중첩된 클래스인 singleInstanceHolder를  기반으로 객체를 선언했기 때문에
한 번만 로드되므로 싱글톤 클래스의 인스턴스는 애플리케이션 당 하나만 존재하며 
클래스가 두 번 로드되지 않기 때문에 두 스레드가 동일한 JVM에서 2개의 인스턴스를 생성할 수 없습니다. 
그렇기 때문에 동기화, 즉 synchronized를 신경쓰지 않아도 됩니다. 

2. final 키워드를 통해서 read only 즉, 다시 값이 할당되지 않도록 했습니다.
3. 중첩클래스 Holder로 만들었기 때문에 싱글톤 클래스가 로드될 때 클래스가 메모리에 로드되지 않고 
어떠한 모듈에서 getInstance()메서드가 호출할 때 싱글톤 객체를 최초로 생성 및 리턴하게 됩니다. 
*/
```

### mongoose의 싱글톤 패턴
Node.js에서 MongoDB 데이터베이스를 연결할 때 쓰는 mongoose모듈에서 싱글톤 패턴을 볼 수 있음.

아래 DB연결을 위해쓰는 mongoose의 connect()함수는 싱들톤 인스턴스를 반환한다.
```javascript
Mongoose.prototype.connect = function(uri, options, callback() {
    const _mongoose = this instanceof Mongoose ? this : mongoose;
    const conn = _mongoose.connection;

    return _mongoose._promiseOrCallback(callback, cb => {
        conn.openUri(uri, options, err => {
            if (err != null) {
                return cb(err);
            }
            return cb(null, _mongoose);
        });
    });
};
```

### MySQL의 싱글톤 패턴
Node.js에서 MySQL DB를 연결할 때도 싱글톤 패턴이 쓰인다.

메인 모듈에서 DB 연결에 관한 인스턴스를 정의하고, 다른 모듈인 A, B에서 해당 인스턴스를 기반으로 쿼리를 보내는 형식을 사용한다.
```javascript
// 메인 모듈
const mysql = require('mysql');

// MySQL 데이터베이스 연결 설정
const connection = mysql.createConnection({
  host: 'localhost',      // 호스트 이름
  user: '사용자이름',     // 사용자 이름
  password: '비밀번호', // 비밀번호
  database: '데이터베이스명' // 데이터베이스 이름
});

connection.connect();

// 모듈 A
connection.query(query, function(error, results, fields) {
    if (error) throw error;
    console.log('The solution is: ', results[0].solution);
});

// 모듈 B
connection.query(query, function(error, results, fields) {
    if (error) throw error;
    console.log('The solution is: ', results[0].solution);
});
```

### 싱글톤 패턴의 단점
TDD(Test Driven Development)에 걸림돌이다. TDD에 주로 단위 테스트를 하는데, 단위 테스트는 서로가 독립적이고 테스트를 어떤 순서로든 실행할 수 있어야 한다.

하지만 싱글톤은 미리 생성된 하나의 인스턴스를 기반으로 구현하는 패턴이기 때문에, 각 테스트마다 **독립적인 인스턴스** 만들기가 어려움.   
</br>

### 의존성 주입
싱글톤이 사용하기 쉽고 실용적이지만, 모듈간의 결합을 강하게 만들 수 있다는 단점이 있다.

이 때 의존성 주입(DI, Dependency Inejction)을 통해 모듈간의 결합을 느슨하게 만들어 해결할 수 있음.

(의존성을 **종속성** 이라고도 함.  A가 B에 의존성이 있다는 것은 B가 변경되면 A도 변경되어야 한다는 뜻)

main module이 직접 하위 모듈에 대한 의존성을 부여하는게 아니라, 중간에 의존성 주입자(dependency injector)가 이 부분을 대신해서, 메인 모듈은 간접적으로 의존성을 주입하는 방식이다. (디커플링이 된다.)   

</br>

- 장점 : 테스팅이 쉽고, 마이그레이션도 수월함. 구현 시 추상화 레이어를 넣고 이를 기반으로 구현체를 넣어주기 때문에, 애플리케이션 의존성 방향이 일관되고 추론이 쉬우며, 모듈간 관계가 명확함.

- 단점 : 모듈이 분리되서 클래스 수가 늘어나서 복잡함. 약간의 런타임 패널티가 있음

- 의존성 주입의 원칙 : 상위 모듈은 하위 모듈에서 어떠한 것도 가져오면 안된다. 둘 다 추상화에 의존해야 하며, 추상화는 세부사항에 의존하면 안된다.







