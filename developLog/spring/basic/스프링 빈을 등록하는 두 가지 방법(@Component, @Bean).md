# 스프링 빈을 등록하는 두 가지 방법(@Component, @Bean)

> 🔗 출처 : [https://dev-coco.tistory.com/69](https://dev-coco.tistory.com/69)

## Bean이 뭘까?

먼저 Bean을 이해하기 위해 스프링 컨테이너(=IoC 컨테이너)에 대해 알 필요가 있다.

자바 어플리케이션은 어**플리케이션 동작을 제공하는 객체들**로 이루어져 있다. 이때, 객체들은 독립적으로 동작하는 것보다 **서로 상호작용**하여 동작하는 경우가 많다.

> 이렇게 **상호작용하는 객체**를 `객체의 의존성`이라고 표현한다.

스프링에서는 스프링 컨테이너에 **객체들을 생성하면 객체끼리 의존성을 주입(DI)하는 역할**을 해주며, **스프링 컨테이너에 등록한 객체**들을 `빈(Bean)`이라고 한다.

## 스프링 컨테이너에 Bean을 등록하는 2가지 방법

### 1. 컴포넌트 스캔과 자동 의존관계 설정

<figure><img src="../../.gitbook/assets/image (12) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (14) (1) (1).png" alt=""><figcaption></figcaption></figure>

스프링 부트에서 사용자 클래스를 스프링 빈으로 등록하는 가장 쉬운 방법은 클래스 선언부 위에`@Component` 어노테이션을 사용하는 것이다.

@Controller, @Service, @Repository는 모두 `@Component`를 포함하고 있으며, 해당 어노테이션으로 **등록된 클래스들**은 **스프링 컨테이너에 의해 자동으로 생성되어 스프링 빈으로 등록**됩니다.

### 2. 자**바 코드로 직접 스프링 빈 등록**

수동으로 스프링 빈을 등록하려면 **자바 설정 클래스**를 만들어 사용해야 한다. XML로 스프링 빈을 등록하는 방법도 있지만, 최근에는 거의 사용하지 않는다고 함

설정 클래스를 만들고 `@Configuration` 어노테이션을 클래스 선언부 위에 추가하면 된다.

그리고 **특정 타입을 리턴하는 메소드**를 만들고, `@Bean` 어노테이션을 붙여주면 **자동으로 해당 타입의 빈 객체가 생성된다.**

<figure><img src="../../.gitbook/assets/image (15) (1) (1).png" alt=""><figcaption></figcaption></figure>

MemberRepository는 **인터페이스**이고, MemoryMemberRepository가 **구현체**이기 때문에  MemoryMemberRepository를 new 해준다.

#### `@Bean` 어노테이션의 주요 내용

* `@Configuration` 설정된 클래스의 메소드에서 사용가능
* 메소드의 리턴 객체가 스프링 빈 객체임을 선언함
* 빈의 이름은 기본적으로 메소드의 이름, @Bean(name="name")으로 이름 변경 가능
* `@Scope`를 통해 **객체 생성을 조정**할 수 있음
* `@Component` 어노테이션을 통해 `@Configuration` 없이도 빈 객체를 생성할 수도 있음
* 빈 객체에 init(), destroy() 등 라이프사이클 메소드를 추가한 다음 @Bean에서 지정할 수 있음
* 어노테이션 하나로 해결되는 1번 방법이 간단하고 많이 사용되고 있지만, 상황에 따라 2번 방법도 사용된다고 한다.
  * 1번방법을 사용해서 개발을 진행하다 **MemberRepository를 변경해야 할 상황이 생기면** 1번 방법은 일일이 변경해줘야 하지만,  2번 방법을 사용하면 다른건 건들일 필요 없이`@Configuration`에 등록된 @Bean만 수정해주면 되므로, 수정이 용이

## 등록된 스프링 빈을 @Autowired로 사용하기

스프링 부트의 경우 @Component, @Service, @Controller, @Repository, @Bean, @Configuration 등으로 **빈들을 등록하고 필요한 곳에서** `@Autowired`를 통해 **의존성 주입을 받아 사용하는 것이 일반적**

### **등록된 빈을 사용하는 3가지 방법**

{% content-ref url="3-field-setter.md" %}
[3-field-setter.md](3-field-setter.md)
{% endcontent-ref %}
