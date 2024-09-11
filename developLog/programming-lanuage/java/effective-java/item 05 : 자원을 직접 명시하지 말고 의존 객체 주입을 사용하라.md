# item 05 : 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

> 많은 클래스가 하나 이상의 자원에 의존한다.

## 잘못 사용하는 예시

`Spellchecker` 클래스를 두 가지 방식으로 정리한 코드

### 정적 유틸리티를 잘못 사용한 예

> 유연하지 않고 테스트 하기 어렵다

```java
import java.util.List;

public class Spellchecker {
    private static final Lexicon dictionary = new Lexicon(); // 사전 초기화

    // 객체 생성 방지
    private Spellchecker() {}

    // 단어의 유효성 검사
    public static boolean isValid(String word) {
        // 구현 내용
        return dictionary.contains(word);
    }

    // 오타에 대한 제안 목록 반환
    public static List<String> suggestions(String typo) {
        // 구현 내용
        return dictionary.getSuggestions(typo);
    }
}
```

* `private` 생성자를 통해 인스턴스화를 방지하고, 모든 메서드는 `static`으로 선언되어 인스턴스를 생성하지 않고 호출할 수 있다.

#### 문제점&#x20;

* **상태 관리의 부재**: 정적 유틸리티 클래스는 상태를 가지지 않기 때문에, 다양한 환경이나 설정에 따라 동작을 변경하기 어렵다. 예를 들어, 서로 다른 언어의 사전을 사용해야 할 경우, 정적 메서드는 이를 지원하지 못한다.
* **테스트의 어려움**: 정적 메서드는 쉽게 모킹(mocking)할 수 없기 때문에, 단위 테스트 작성이 어려워진다. 외부 의존성(예: 사전 데이터베이스)을 가진 메서드를 테스트할 때, 실제 데이터가 필요하게 되어 테스트가 복잡해질 수 있다.
* **확장성의 제한**: 유틸리티 메서드가 고정된 형태로 존재하기 때문에, 새로운 기능을 추가하거나 수정할 때 코드의 변경이 필요하며, 기존 코드와의 호환성 문제가 발생할 수 있다.

### 싱글턴 패턴 방식을 잘못 사용한 예

> 유연하지 않고 테스트 하기 어렵다.

```java
import java.util.List;

public class Spellchecker {
    private static final Spellchecker INSTANCE = new Spellchecker(); // 단일 인스턴스
    private final Lexicon dictionary = new Lexicon(); // 사전 초기화

    // 객체 생성 방지
    private Spellchecker() {}

    // 싱글턴 인스턴스 반환
    public static Spellchecker getInstance() {
        return INSTANCE;
    }

    // 단어의 유효성 검사
    public boolean isValid(String word) {
        // 구현 내용
        return dictionary.contains(word);
    }

    // 오타에 대한 제안 목록 반환
    public List<String> suggestions(String typo) {
        // 구현 내용
        return dictionary.getSuggestions(typo);
    }
}
```

#### 설명

* `private` 생성자를 사용하여 인스턴스화를 방지하고, 정적 필드 `INSTANCE`를 통해 단 하나의 인스턴스를 제공한다. `getInstance()` 메서드를 통해 이 인스턴스를 반환한다.

#### 문제점&#x20;

* **전역 상태**: 싱글턴 인스턴스가 애플리케이션 전역에서 공유되기 때문에, 상태를 가지게 된다. 이로 인해 여러 테스트가 서로 영향을 미칠 수 있으며, 테스트가 독립적으로 수행되지 않게 된다.
* **테스트의 복잡성**: 싱글턴 클래스의 상태를 초기화하거나 변경하기 어려워, 테스트 환경에서 일관성을 유지하기 힘들다. 예를 들어, 특정 테스트 케이스에서 사전 내용을 변경해야 할 경우, 다른 테스트에 영향을 줄 수 있다.
* **유연성 부족**: 필요에 따라 다른 사전(예: 다양한 언어의 사전이나 테스트용 사전)을 사용하고 싶어도, 단일 인스턴스에 고정되어 있어 유연성이 떨어진다. 새로운 인스턴스를 생성하거나 다른 설정을 적용하기 어렵다.

> 정적 유틸리티와 싱글턴 패턴 모두 특정 상황에서는 유용할 수 있지만, 위와 같은 이유로 인해 유연성과 테스트 용이성을 저하시킬 수 있다. 특히, <mark style="color:red;">사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.</mark> 자원들을 클래스가 직접 만들게 해서도 안 된다. 대신 필요한 자원을 (혹은 그 자원을 만들어주는 팩터리를) 생성자에 (혹은 정적 팩터리나 빌더에) 넘겨주자. `의존 객체 주입`이라 하는 이 기법은 클 래스의 유연성, 재사용성, 테스트 용이성을 기막히게 개선해준다.

## 제대로 사용하는 예 : 의존 객체 주입 패턴

클래스(Spellchecker)가 여러 자원 인스턴스를 지원해야 하며, **클라이언트가 원하는 자원(dictionary)을 사용해야 한다.** <mark style="color:red;">인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식</mark>이다.&#x20;

### 예시 1 : Spellchecker 클래스

* Spellchecker 클래스의 예시로, Lexicon 객체를 생성자에 주입받는 구조
* `의존 객체 주입(Dependency Injection) 패턴`은 클래스의 인스턴스를 생성할 때 필요한 자원을 생성자에 전달하는 방식으로, 유연성과 테스트 용이성을 높여준다.

```java
import java.util.List;
import java.util.Objects;

public class Spellchecker {
    private final Lexicon dictionary;

    // 생성자에서 의존 객체인 Lexicon을 주입받음
    public Spellchecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }

    // 단어의 유효성 검사
    public boolean isValid(String word) {
        return dictionary.contains(word);
    }

    // 오타에 대한 제안 목록 반환
    public List<String> suggestions(String typo) {
        return dictionary.getSuggestions(typo);
    }
}
```

* 예에서는 dictionary라는 딱 하나의 자원 만 사용하지만, 자원이 몇 개든 의존 관계가 어떻든 상관없이 잘 작동한다.
* `불변(아이템 17)을 보장`하여 (같은 자원을 사용하려는) <mark style="color:red;">여러 클라이언트가 의존 객체들을 안심하고 공유할 수 있기도 하다.</mark>
* 의존 객체 주입은 생성자, 정적팩터리(아이템 1), 빌더(아이템 2) 모두에 똑같이 응용할 수 있다.

### 예시 2: 팩터리 메서드 패턴

* 의존 객체 주입의 변형으로, 생성자에 자원 팩터리를 넘겨주는 방식이 있다.&#x20;
* 팩터리는 **호출할 때마다 특정 타입의 인스턴스를 생성하는 객체**이다. 자바 8의 `Supplier<T>` 인터페이스를 사용하여 이를 표현할 수 있다.

#### 예시 코드: Mosaic 클래스와 팩터리 메서드

```java
import java.util.function.Supplier;

public class Mosaic {
    // 클라이언트가 제공한 팩터리를 사용하여 Mosaic 생성
    public static Mosaic create(Supplier<? extends Tile> tileFactory) {
        // 타일 생성 및 모자이크 구성 로직
        // 예시: Tile tile = tileFactory.get();
        return new Mosaic();
    }
}

// 예시 Tile 클래스
class Tile {
    // Tile 관련 구현
}
```

* **유연성**: 의존 객체 주입을 통해 다양한 자원을 쉽게 교체할 수 있어 유연성이 증가한다.
* **테스트 용이성**: 테스트 시 모킹(mocking)을 통해 의존성을 쉽게 조작할 수 있어, 테스트가 간편
* **팩터리 메서드 패턴**: 클라이언트가 제공한 팩터리를 사용하여 인스턴스를 생성하는 방식으로, 다양한 하위 타입의 인스턴스를 생성할 수 있다.

의존 객체 주입이 유연성과 테스트 용이성을 개선해주긴 하지만, 의존성이 수 천 개나 되는 큰 프로젝트에서는 코드를 어지럽게 만들기도 한다. 의존성이 많아질 경우, Dagger, Guice, Spring과 같은 의존 객체 주입 프레임워크를 활용하면 더욱 효과적으로 관리할 수 있다.



