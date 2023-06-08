# 1.1 Design Patterns

- [1.1.1 싱글톤 패턴](#1.1.1-싱글톤-패턴)
  - [싱글톤 패턴 예시](#데이터베이스-연결-모듈)
  - [싱글톤 패턴의 단점](#싱글톤-패턴의-단점)
  - [의존성 주입](#의존성-주입)
- [1.1.2 팩토리 패턴](#1.1.2-팩토리-패턴)
  - [자바스크립트의 팩토리 패턴](#자바스크립트의-팩토리-패턴)
  - [자바의 팩토리 패턴](#자바의-팩토리-패턴)
  - [Java의 Enum](#Java의-Enum)


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



</br>

---
# 1.1.2 팩토리 패턴
## 팩토리 패턴(factory pattern)
- 객체를 사용하는 코드에서 객체 생성부분을 떼어내 추상화한 패턴
- 상속관계에 있는 두 클래스 중 상위 클래스가 뼈대 결정, 하위 클래스가 객체 생성에 대한 구체적인 내용을 결정

- 장점
  - 상위/하위가 분리되기 때문에 느슨한 결합을 가짐.
  - 상위 클래스에서 인스턴스 생성 방식에 대해 몰라도 되서 유연함. 
  - 유지보수에 용이. 코드 리팩토링 시에도 생성부분이 떼어져 있어서 한 곳만 고칠 수 있음

ex. 라떼 레시피, 아메리카노 레시피, 우유 레시피 (하위 클래스) -> 바리스타 공장(상위 클래스)

<br/>


### 자바스크립트의 팩토리 패턴
```javascript
class Latte {
    constructor() {
        this.name = "Latte"
    }
}

class Espresso {
    constructor() {
        this.name = "Espresso"
    }
}

class LatteFactory {
    static createCoffee() {
        return new Latte()
    }
}

class EspressoFactory {
    static createCoffee() {
        return new Espresso()
    }
}


const factoryList = { LatteFactory, EspressoFactory }

class CoffeeFactory {
    // static 키워드를 통해 정적 메소드 선언 - 클래스 기반으로 객체를 만들지 않고 호출 가능
    // 해당 메서드에 메모리 할당을 한 번만 할 수 있는 장점이 있다.
    static createCoffee(type) {
        const factory = factoryList[type]
        return factory.createCoffee
    }
}

const main = () => {
    // 라떼 커피를 주문한다.
    // 의존성 주입 - LatteFactory에서 생성한 인스턴스를 CoffeeFactory에 주입
    const coffee = CoffeeFactory.createCoffee("LatteFactory")
    // 커피 이름 호출
    console.log(coffee.name) // latte
}

main()
```

### 자바의 팩토리 패턴

1. abstract class인 Coffee를 만들고, getPrice라는 public abstract 메서드도 정의한다.
2. extends키워드로 Coffee를 상속받은 DefaultCoffee, Latte, Americano 를 정의한다.
3. class CoffeeFactory 를 만들고, 내부에 Coffee를 리턴하는 static 함수인 getCoffee(String type, int price) 메서드를 만든다.
<br/>(그리고 getCoffee 호출 할때 type에 원하는 커피 이름을 넣으면 완성!)

<br/>

### Java의 Enum

- 자바는 enum에 상수 뿐만 아니라 메서드를 집어넣어 관리 가능
- enum으로 관리 시 장점
  - 리팩토링 중 상수 집합 로직 수정 시 이 부분만 수정하면 됨,
  - thread safe 해서 싱글톤 패턴을 만들 때 도움이 됨.


```java
// enum으로 만든 싱글톤 예시
public enum SingletonEnum {
    INSTANCE;

    // 싱글톤 클래스의 멤버 변수나 메서드를 추가할 수 있음
    private int value;

    public int getValue() {
        return value;
    }

    public void setValue(int value) {
        this.value = value;
    }
}
```
위의 코드에서는 SingletonEnum이라는 Enum을 정의하고, INSTANCE라는 단일 열거 상수를 선언한다.

이 Enum은 자체적으로 싱글톤으로 정의되며, 어디서든 접근할 수 있는 단일 인스턴스를 보장한다.
```java
SingletonEnum instance = SingletonEnum.INSTANCE;
instance.setValue(10);
System.out.println(instance.getValue()); // 출력: 10
```



