# JPA란 무엇인가요?

> 🔗 강의링크 : [https://www.inflearn.com/course/스프링부트-개념정리/dashboard](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%EA%B0%9C%EB%85%90%EC%A0%95%EB%A6%AC/dashboard)

## 1. JPA는 Java Persistence API이다.

> `JPA`라는 것은 Java Persistence Application Programming interface로 자**바 프로그래밍을 할 때, 영구적으로 필요한 인터페이스**

### ⭐ 영속성(persistence)란?

데이터를 생성한 프로그램의 실행이 종료되더라도 **사라지지 않는 데이터의 특성을 의미**하며, 영속성은 파일 시스템, 관계형 데이터베이스 혹은 객체 데이터베이스 등을 활용하여 구현한다.

영속성을 예시로 들자면 `RAM`에 경우 **휘발성**이라 컴퓨터가 꺼지게 되면 사라지게 되는데 RAM에 대한 데이터들을 `하드디스크`에 저장하게 되면 하드디스크는 **비휘발성**이라서 사라지지 않는다.

![](https://velog.velcdn.com/images/prettylee620/post/1e679de6-6450-4868-a60e-eef7a5018877/image.png)

데이터베이스가 관리할 수 있도록 DBMS로 관리한다.

즉, `JPA`란.. **자바에서 영구히 기록할 수 있는 환경을 제공하는 API**를 말한다.

### ⭐ API란?

* 애플리케이션 (A) ⇒ 프로그램
* 프로그래밍(P) ⇒ 프로그래밍
* 인터페이스(I) ⇒ 인터페이스

> 인터페이스로 프로그래밍을 하고 그걸로 프로그램이 만들어지는 것을 뜻한다.

### ⭐ 프로토콜 VS 인터페이스

1. **프로토콜과 인터페이스의 공통점**

둘 다 `약속`을 뜻한다.

2. **차이점**

*   인터페이스

    > 상하 관계가 존재하는 약속

    * 예시로 들자면 장보고가 프로그램을 만들었다. 근데 프로그램이 좋아서 공유를 하고 싶다. 그래서 홍길동한테 공유를 하는데 \*\*“A데이터를 사용하려면 밤 12시부터 새벽 6시까지 사용해라”\*\*고 상의없이 말했다. 이것이 바로 `인터페이스`
    * 인터페이스를 통해 데이터를 받아 프로그램을 만들면 그것이 바로 `API`
*   프로토콜

    > 동등한 관계에 약속

    * 수 많은 프로토콜들이 모여서 만들어진 것이 인터

## 2. JPA는 ORM(Obeject Relational Mapping) 기술이다.

건물을 짓는 설계도로 건물을 올리게 되면 3D로 나오게 되는 것을 `모델링`이라고 한다.

> 모델링 한다는 것은 **추상화적인 개념을 현실세계에 구체화하는 것**을 말한다.

![](https://velog.velcdn.com/images/prettylee620/post/17d3b0e1-3798-473e-b183-ea7b66266102/image.png)

자바가 가지고 있는 데이터 타입과 데이터베이스가 가지고 있는 데이터 타입은 다르다. `class`를 통해서 **데이터베이스에 있는 테이블을 모델링해야 한다.**

원래대로라면, **첫 번째**로는 DB 테이블을 만들고 **2번째**로 자바세상에 모델링한다. ⇒ TRM(Table Relational Mapping)

```java
class Team{
	int id;
	String name;
	String year;
}
```

> 하지만, `JPA`경우 **첫 번째**로 자바에서 코드를 작성한 것인 클래스를 만들어서 **두 번째**로 데이터베이스 테이블을 자동으로 생성 수 있다. 이때 필요한 것이 인터페이스이다.

## 3. JPA는 반복적인 CRUD 작업을 생략하게 해준다.

Select나 Select All, Delete, Update, Insert 등은 자주 일어날 수 있는 것들이다.

java의 경우 **DB에게 Connect 요청**을 한다. `DB가 권한을 확인`하고 **Session을 오픈**하게 해준다. 그렇게 되면 java는 Conncection을 가지며, 두 번째에는 **쿼리를** DB에 전송할 수 있다. DB는 그것을 기반으로 Data를 java에 응답하게 되는데 **이때, 자바가 가진 데이터 타입과 DB가 가진 데이터 타입은 다르다.**

![](https://velog.velcdn.com/images/prettylee620/post/31be9988-10af-4d40-bba4-a47f9106f1d7/image.png)

즉, **자바 Object로 변환**을 해야 하는데 이것은 `단순한 반복로직`이다. 이러한 과정들을 connction을 연결하고 변환하고 등등을 **JPA가 하나의 함수로 제공**해준다.

## 4. JPA는 영속성 컨텍스트를 가지고 있다.

#### 영속성이란

데이터 ⇒ 영구적 저장(파일시스템 X, DB O)

### 1) 컨텍스트란?(Context)

> Context란 대상의 모든 정보를 가지고 있는 것

개발에서 컨텍스트 개념은 조금 애매하다. 이렇게 이해하면 좋다고 한다. 길동이가 영숙이에게 **난 영숙이 너의 모든 컨텍스트를 가지고 있어 = 영숙이의 모든 것을 알고 있다.**

Context를 넘겨준다. = 보고가 길동이에게 영숙이의 Context(모든 정보)를 넘겨

### 2) 영속성 컨텍스트란?

자바가 **데이터베이스를 저장해야 하는 모든 데이터를 아는 것**이 영속성 컨텍스트

자바에 있는 동물 데이터를 영속성 컨텍스트에 넣고 나서 DB에도 저장 후 **영속성 컨텍스트에서 삭제**를 하면 `동기화`가 되어 있기에 **DB에서도 삭제가 된다**.

![](https://velog.velcdn.com/images/prettylee620/post/f1f95312-16bf-4de9-9788-3c2ae22ac824/image.png)

영속성 컨텍스트에는 없고 DB에만 과일 데이터가 있다면..

일단 자바는 1. 영속성 컨텍스트에 먼저 `요청`을 한다. 2. 영속성 컨텍스트에 데이터가 없기에 `DB에 요청` 한다. 3. DB에서 가져와 **자바 object로 영속성컨텍스트에 저장**한다. 4. 자바에 과일데이터를 준다.

![](https://velog.velcdn.com/images/prettylee620/post/10811ad8-c835-4dbc-bd50-589ffe9f3a54/image.png)

> 데이터 베이스에 있는 **데이터를 select해서 가져오고 update하는 등 일련의 모든 정보**를 `영속성 컨텍스트`를 통해 확인 할 수 있다.

**자바가 데이터베이스에 저장해야 하는 모든 메타 데이터 정보**들을 영속성 컨텍스트가 가지고 있다.

java에서 과일 데이터를 딸기로 바꾸면..

![](https://velog.velcdn.com/images/prettylee620/post/b38f2a03-666e-4237-8f2c-c4c411a25015/image.png)

1. 영속성 컨텍스트의 과일데이터도 딸기로 바뀌고
2. 데이터베이스에 commit해서 밀어넣으면 원래 셋은 같은 데이터 인데 3번은 사과로 되어 있어 딸기로 바꾸기위해 `update`가 자동으로 일어난다.

## 5. JPA는 DB와 OOP의 불일치성을 해결하기 위한 방법론을 제공한다.(DB는 객체저장 불가)

![](https://velog.velcdn.com/images/prettylee620/post/98e2b0b4-a1b6-4463-9bdb-4650f3f6ffe6/image.png)

* DB는 기본자료형으로 Object를 가지지 못함
  * teamId의 경우 1이라는 숫자를 가지고 있지 아래의 그림 처럼 object채로 가지고 있지 못한다.

![](https://velog.velcdn.com/images/prettylee620/post/5e8ec78a-15c0-49a7-a3e6-7d52e7e26b7c/image.png)

* 자바 관점 : 일반 사용자는 알 수 없기에 join을 하거나 두 번의 select가 필요

```java
class Team{
	int id;
	String name;
	String year;
}

class Player{
	int id; //=2
	String name; //=공필성
	int teamId; //=1
}
```

그렇지만.. 자바는 Object를 저장할 수 있기에 아래와 같이 만들 수 있다.

```java
class Player{
	int id; //=2
	String name; //=공필성
	Team team; //=team object
}
```

즉, `OOP`를 만들 수 있게 된다. 객체 지향적으로 만들 수 있다는 것이다. 그리고 `ORM`을 하게 되면 **자바가 주도권을 가진 즉, 위와 같은 형태의 데이터 베이스를 만들 수 있다.**

또한.. JPA가 알아서 Mapping 해서 select, insert등을 해주게 된다.

> 즉, JPA는 DB와 OOP의 불일치성을 해결하기 위한 방법론을 제공한다는 것은 먼저, 데이터베이스가 주도권을 잡고 있다면 데이터베이스는 Object를 저장할 수 없기에 OOP와 불일치하게 되지만 JPA를 사용하게 되면 `OOP`를 만들 수 있게 된다. 객체 지향적으로 만들 수 있다는 것이다. 그리고 `ORM`을 하게 되면 **자바가 주도권을 가진 즉, 위와 같은 형태의 데이터 베이스를 만들 수 있다.**

## 6. JPA는 OOP 관점에서 모델링을 할 수 있게 해준다.(상속, 콤포지션, 연관관계)

```java
class Car{
	int id; //pk
	String name;
	String color;
	Engine engine;
}

class Engine{
	int id;
	int power;
}
```

⇒ 상속으로 되지는 않고 `콤포지션(결합)`해야 한다.

![](https://velog.velcdn.com/images/prettylee620/post/f2c0a850-c12b-4c74-b473-aed82dc24b43/image.png)

jpa의 경우 class를 만든 다음 **데이터베이스 자동생성**해준다. 1번이 생성 후 2번이 자동생성된다.

> 이렇게 되면 `OOP 관점`(객체 지향 프로그래밍)에서 테이블이 만들어지게 된다.

Engine과 Car 클래스에 모두 다 TimeStamp createDate;와 TimeStamp updateDate;를 넣고 싶다면

그 부분은 **반복된 것**이기 때문에 각 클래스에 넣는 것이 아닌 **반복된 것을 다른 클래스로 만들어준다.**

```java
class EntityDate{
	TimeStamp createDate;
	TimeStamp updateDate;
}
```

후에 그 클래스를 상속받으면 된다.

```java
class Car extends EntityDate{
	int id; //pk
	String name;
	String color;
	Engine engine;
}

class Engine extends EntityDate{
	int id;
	int power;
}
```

`상속`을 받게 되면 옆에 필드가 더 생긴다.

![](https://velog.velcdn.com/images/prettylee620/post/659dfd6f-1102-484b-a637-ce10dd015abe/image.png)

연관관계의 경우 두 객체(엔터티) 간의 관계를 나타내는데

#### 연관 관계 표현

1. **일대일(One-to-One):** 예를 들어, 사용자(User)와 프로필(Profile)이라는 엔터티가 있을 때, 한 사용자는 하나의 프로필을 가지고, 한 프로필은 하나의 사용자에 속할 수 있다.
2. **일대다(One-to-Many):** 예를 들어, 부서(Department)와 사원(Employee)이라는 엔터티가 있을 때, 하나의 부서는 여러 사원을 가질 수 있다.
3. **다대일(Many-to-One):** 일대다의 반대로 여러 엔터티가 하나의 엔터티에 속하는 관계
4. **다대다(Many-to-Many):** 예를 들어, 학생(Student)과 과목(Subject)이라는 엔터티가 있을 때, 한 학생은 여러 과목을 수강하고, 한 과목은 여러 학생에게 수강될 수 있다.

#### 표현방법 : 어노테이션

**`@OneToOne`**, **`@OneToMany`**, **`@ManyToOne`**, **`@ManyToMany`** 등의 어노테이션을 사용하여 이러한 연관관계를 매핑할 수 있다.

연관관계를 설정하면 객체 간의 관계를 유지하면서 데이터베이스에도 적절한 계가 맺어지게 된다.

## 7. 방언 처리가 용이하여 Migration하기 좋다. 유지보수에 좋다.

jpa는 clialect에 대한 것들을 많이 가지고 있다.(oracle, maria, mssql, mysql, postgam)

또한 DB는 `추상화객체`가 중간에 있어서 데이터베이스 변경에도 용이하다. 내가 만약 오라클을 쓰다 MYSQL로 바꾸고 싶어도 중간에 추상화 객체가 있기에 무리없이 바꿀 수 있다.

즉, 방언 처리가 용이하기 좋다고 한다.

![](https://velog.velcdn.com/images/prettylee620/post/da6e56f7-6dc3-48e0-85ec-e9f07bcd1c21/image.png)

![](https://velog.velcdn.com/images/prettylee620/post/68b540cb-7d2c-4d51-b2f6-fa90eee2fd60/image.png)

### 1) JPA의 추상화 객체

> 추상화 객체는 `엔터티(Entity)`를 나타내는 객체로 이 객체들은 데이터베이스의 테이블과 매핑되어 있어서 객체 지향 프로그래밍에서 사용되는 모델과 데이터베이스 스키마 간의 매핑을 편리하게 할 수 있도록 도와준다.

1. **@Entity:** **클래스를 엔터티로 지정하는 어노테이션**으로 해당 클래스의 객체가 데이터베이스의 레코드에 매핑
2. **@Id:** 엔터티의 `주 식별자(Primary Key)`를 지정하는 어노테이션으로 주키는 **해당 엔터티를 고유하게 식별하는데 사용**
3. **@GeneratedValue:** **주 키의 값을 자동으로 생성**하도록 지정하는 어노테이션
4. **@Column:** **엔터티의 필드를 데이터베이스의 컬럼과 매핑**할 때 사용되는 어노테이션
5. **@ManyToOne, @OneToMany, @OneToOne, @ManyToMany:** 관계형 데이터베이스에서의 다양한 관계를 표현하기 위한 어노테이션

### 2) Migration(마이그레이션이란)

데이터베이스 **스키마의 변경을 관리하는 프로세스**를 의미한다. 애플리케이션의 요구사항이나 업데이트로 인해 데이터베이스의 구조가 변경되어야 할 때, 마이그레이션을 통해 기존 데이터베이스를 새로운 구조로 업데이트할 수 있다.

JPA에서는 **데이터베이스 스키마를 업데이트하는 도구**로 `Hibernate`와 같은 **ORM 도구**를 사용한다. 이 도구들은 자동으로 데이터베이스 스키마를 생성하거나 업데이트할 수 있어서 개발자가 직접 SQL 스크립트를 작성하지 않아도 된다. 이를 통해 객체 지향 모델과 데이터베이스 스키마 간의 **일관성**을 유지하면서도 변경이 일어날 수 있다.

### 3) Hibernate

자바 언어를 위한 `ORM(Object-Relational Mapping)`**프레임워크** 중 하나로, ORM은 객체 지향 프로그래밍에서 사용되는 객체와 관계형 데이터베이스 간의 매핑을 자동으로 처리해주는 도구이다. `Hibernate`는 **이러한 매핑을 통해 개발자가 SQL 쿼리를 직접 작성하지 않고도 데이터베이스와 상호 작용할 수 있도록 도와준다.**

Hibernate의 주요 특징과 기능은 다음과 같습니다:

1. **객체-관계 매핑:** Hibernate는 자바 객체와 데이터베이스 테이블 간의 매핑을 지원한다. 이를 통해 객체 지향 프로그래밍 언어인 자바에서 관계형 데이터베이스를 더 편리하게 다룰 수 있다.
2. **데이터베이스 독립성:** Hibernate는 사용하는 데이터베이스에 대한 종속성을 낮춰준다. 즉, 데이터베이스를 변경하더라도 코드 변경이 최소화
3. **자동 테이블 생성 및 업데이트:** Hibernate는 객체 모델을 기반으로 데이터베이스 테이블을 자동으로 생성하거나 업데이트할 수 있다. 이는 개발자가 직접 SQL을 작성하지 않아도 되게 해준다.
4. **캐싱:** Hibernate는 성능 향상을 위해 캐싱을 지원한다. 쿼리 결과나 객체 상태를 캐시하여 반복적인 데이터베이스 액세스를 최소화할 수 있다.
5. **트랜잭션 관리:** Hibernate는 데이터베이스 트랜잭션을 관리하는데 도움을 준다. ACID 속성을 유지하면서 트랜잭션을 제어할 수 있다.
6. **쿼리 언어 지원:** Hibernate는 HQL(Hibernate Query Language)라는 객체 지향적인 쿼리 언어를 제공하여 SQL에 종속되지 않고도 데이터베이스에 접근할 수 있다.

Hibernate는 자바 개발자들 사이에서 매우 인기 있는 ORM 프레임워크 중 하나로, 데이터베이스와의 상호 작용을 간소화하고 생산성을 향상시킬 수 있는 강력한 도구다.

## 8. JPA는 쉽지만 어렵다.
