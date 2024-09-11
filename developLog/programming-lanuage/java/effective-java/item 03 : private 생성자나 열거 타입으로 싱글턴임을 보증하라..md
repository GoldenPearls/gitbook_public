# item 03 : private 생성자나 열거 타입으로 싱글턴임을 보증하라.

## 싱글턴임을 보증하라

### 싱글턴(singleton)이란?

* <mark style="color:red;">인스턴스를 오직 하나만 생성할 수 있는 클래스</mark>를 말한다.
* 전형적인 예로는 함수와 같은 무상태(stateless) 객체나 설계상 유일해야 하는 시스템 컴포넌트

> 클래스를 싱글턴으로 만들면 **이를 사용하는 클라이언트를 테스트하기가 어려워질 수 있다.**

이유는 타입을 `인터페이스`**로 정의한 다음 그 인터페이스를 구현해서 만든 싱글턴이 아니라면** 싱글턴 인스턴스를 **가짜(MOCK) 구현으로 대체할 수 없기 때문**이다.

### **싱글턴을 만드는 방식**

> 두 방식 모두 **생성자는 private**으로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 `public static 멤버`를 하나 마련

### 방식 1 :  publioc static 멤버가 final 필드인 방식

```java
public class Elvis {
    // 유일한 인스턴스를 생성
    public static final Elvis INSTANCE = new Elvis();

    // 생성자를 private으로 설정하여 외부에서 인스턴스를 생성하지 못하게 함
    private Elvis() {
        // 초기화 코드 (필요한 경우)
    }

    // Elvis가 떠나는 행동을 정의하는 메서드
    public void leaveTheBuilding() {
        // 구현 코드
    }
}
```

private 생성자는 public static final 필드인 **Elvis.INSTANCE를 초기화할 때 딱 한 번만 호출**된다. public이나 protected 생성자가 없으므로 Elvis 클래스가 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.&#x20;

#### 클라이언트 생성자 호출 방법

* **리플렉션 API**: Java의 리플렉션 API는 런타임에 클래스, 메서드, 필드 등의 정보를 동적으로 조사하고 조작할 수 있는 기능을 제공
* **AccessibleObject.setAccessible**: 이 메서드는 `private` 또는 `protected`로 설정된 필드나 메서드에 접근할 수 있게 해준다. 즉, 일반적으로 접근이 제한된 요소에 접근할 수 있도록 허용한다.
* 권한이 있는 클라이언트(예: 특정 설정이나 보안 권한을 가진 코드)는 리플렉션을 사용하여 `private` 생성자를 호출할 수 있다. 이 경우, 싱글턴 패턴의 의도가 무시되고, 여러 개의 인스턴스가 생성될 수 있다.

> 이러한 공격을 방어하려면, 생성자를 수정하여 **두 번째 객체가 생성되려 할 때 예외**를 던지게 하면 됨

#### 장점

* public 필드 방식의 큰 장점은 해당 클래스가 싱글턴임이 API에 명백히 드러난다는 것이다.
* public static 필드가 final이니 절대로 다른 객체를 참조할 수 없다.
* 간결하다.

### 방식 2 : 정적 팩터리 메서드를 public static 멤버로 제공

```java
public class Elvis {
    // 유일한 인스턴스를 생성
    private static final Elvis INSTANCE = new Elvis();

    // private 생성자
    private Elvis() {
        // 초기화 코드 (필요한 경우)
    }

    // 인스턴스를 반환하는 메서드(새로)추가됨
    public static Elvis getInstance() {
        return INSTANCE;
    }

    // Elvis가 떠나는 행동을 정의하는 메서드
    public void leaveTheBuilding() {
        // 구현 코드
    }
}

```

`Elvis.getInstance` 는 항상 같은 객체의 참조를 반환하므로 제2의 Elvis 인스턴스란 결코 만들어지지 않다（역시 리플렉션을 통한 예외는 똑같이 적용된다）

#### 장점

* (마음이 바뀌면) **API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다는 점 :** 유일하게 인스턴스를 반환하던 팩터리 메서드가 호출하는 스레드별로 다른 인스턴스를 넘겨주게 할 수 있음

```java
import java.util.HashMap;
import java.util.Map;

public class Elvis {
    // 인스턴스를 저장할 맵
    private static final Map<Long, Elvis> instances = new HashMap<>();

    // private 생성자
    private Elvis() {
        // 초기화 코드
    }

    // 정적 메서드: 스레드 ID를 기준으로 인스턴스를 반환
    public static Elvis getInstance() {
        long threadId = Thread.currentThread().getId();
        return instances.computeIfAbsent(threadId, id -> new Elvis());
    }

    public void leaveTheBuilding() {
        // 행동 구현
    }
}
```

1. **인스턴스 저장소**: `instances`라는 `Map`을 사용하여 각 스레드에 대한 인스턴스를 저장한다. 스레드 ID를 키로 사용한다.
2. **정적 메서드**: `getInstance()` 메서드는 현재 스레드의 ID를 가져와서, 해당 스레드에 대한 인스턴스가 이미 존재하는지 확인한다. 존재하지 않으면 새 인스턴스를 생성하고 저장
3. **유연성**: 이 구현은 API를 변경하지 않고도, 사용자가 원할 경우 스레드마다 다른 인스턴스를 반환할 수 있게 한다. 즉, 각 스레드는 독립적으로 `Elvis` 인스턴스를 가질 수 있다.

* 원한다면 정적 팩터리를 `제네릭 싱글턴 팩터리`로 만들 수 있다. : 정적 팩터리의 메서드 참조를 `공급자(supplier)`로 사용할 수 있다는 점이다.

```java
import java.util.function.Supplier;

public class Elvis {
    private static final Map<Long, Elvis> instances = new HashMap<>();

    private Elvis() {
        // 초기화 코드
    }

    public static Elvis getInstance(Supplier<Elvis> supplier) {
        long threadId = Thread.currentThread().getId();
        return instances.computeIfAbsent(threadId, id -> supplier.get());
    }

    public void leaveTheBuilding() {
        // 행동 구현
    }
}

// 사용 예시
public class Main {
    public static void main(String[] args) {
        Elvis elvis = Elvis.getInstance(Elvis::new); // 공급자로서의 메서드 참조
        elvis.leaveTheBuilding();
    }
}
```

> 🧐  참고로 왜 API를 바꾸지 않고도 싱글턴을 변경하는 방법, API를 바꾸지 않고도 여러 인스턴스를 반환할 수 있도록 변경하는 것은 결국 **코드를 바꾸는 건데 의미가 있어?**

**AI  답변**

좋은 질문입니다. "API를 바꾸지 않고도"라는 표현은 기존 인터페이스(메서드 시그니처 등)를 유지하면서 내부 구현을 변경할 수 있다는 의미입니다. 즉, 외부에서 사용하는 코드에 영향을 주지 않고, 클래스의 동작 방식을 바꿀 수 있다는 점에서 의미가 있습니다.

#### 의미와 장점

1. **호환성 유지**: 기존 코드를 사용하는 다른 부분은 변경할 필요가 없으므로, 시스템의 안정성을 유지할 수 있습니다. 즉, 외부에서 `getInstance()` 호출 방식은 그대로 유지됩니다.
2. **유연성**: 내부적으로 스레드별 인스턴스를 반환하는 방식으로 변경함으로써, 필요에 따라 여러 인스턴스를 제공할 수 있습니다. 이는 새로운 요구사항이 생길 때 유용합니다.
3. **확장성**: 나중에 더 복잡한 로직이나 상태 관리가 필요할 때, API를 변경하지 않고도 기능을 추가하거나 수정할 수 있습니다.

#### 예시

예를 들어, 기존에 `Elvis.getInstance()`를 사용하던 코드가 있다고 가정해 보겠습니다. 이 코드는 변경하지 않고도, 내부 구현을 통해 스레드별로 다른 인스턴스를 관리하게 할 수 있습니다.

```java
Elvis elvis1 = Elvis.getInstance();
Elvis elvis2 = Elvis.getInstance();
```

위 코드는 여전히 동일하게 작동하지만, 내부 구현에서는 이제 각 스레드에 대해 다른 `Elvis` 인스턴스를 제공하게 됩니다. 외부 코드는 전혀 변경되지 않지만, 내부 동작은 훨씬 더 유연해진 것입니다.

#### 결론

결론적으로, API를 변경하지 않고도 내부 구현을 수정하는 것은 코드의 유연성과 유지보수성을 높이는 좋은 방법입니다. 이는 장기적으로 시스템의 확장성과 안정성을 보장하는 데 큰 도움이 됩니다.



### 싱글턴(Singleton) 패턴을 구현하는 방법과 직렬화(Serialization) 문제

#### 1. 직렬화와 싱글턴

* **Serializable 구현**: 싱글턴 클래스를 직렬화하려면 `Serializable` 인터페이스를 구현해야 한다. 그러나 이 인터페이스를 구현하는 것만으로는 충분하지 않다.
* **transient 필드**: 모든 인스턴스 필드를 `transient`로 선언해야 하며, 이는 직렬화 시 해당 필드가 저장되지 않도록 한다.
*   **readResolve 메서드**: 직렬화된 객체를 역직렬화할 때마다 새로운 인스턴스가 생성되는 것을 방지하기 위해 `readResolve` 메서드를 제공한다. 이 메서드는 역직렬화 과정에서 호출되어, 진짜 싱글턴 인스턴스를 반환하게 된다.

    ```java
    //싱글턴임을 보장해주는 readResolve 메서드
    private Object readResolve() {    
        return INSTANCE; // 진짜 Elvis 인스턴스를 반환 가짜 Elvis는 가비지 컬렉터에게 
    }
    ```

    이 메서드는 가짜 `Elvis` 인스턴스가 생성되는 것을 방지하고, 기존의 `INSTANCE`를 반환하여 원래의 싱글턴을 유지한다. <mark style="color:red;">특정 클래스에서 사용할 수 있으며,</mark> 직렬화와 역직렬화를 지원하는 클래스에서 `readResolve` 메서드를 정의하면, 해당 클래스의 인스턴스가 역직렬화될 때 호출된다.

> ✔️  "진짜 Elvis 인스턴스를 반환하고 가짜 Elvis는 가비지 컬렉터에게"라는 표현
>
> 주로 싱글턴 패턴을 설명할 때 사용된다. 여기서 "진짜 Elvis"는 실제로 사용해야 하는 유일한 인스턴스를 의미하고, "가짜 Elvis"는 불필요하게 생성된 인스턴스를 의미합
>
> 이 문구는 `readResolve` 메서드의 역할을 강조한다. 역직렬화 과정에서 `readResolve` 메서드가 호출되면, 가짜 인스턴스가 아닌 진짜 인스턴스를 반환하여, 불필요한 인스턴스가 생성되는 것을 방지한다. 가짜 인스턴스는 더 이상 필요 없게 되어 가비지 컬렉터에 의해 수거될 수 있다. **즉, 메모리 관리 측면에서 불필요한 객체를 제거하여 자원을 효율적으로 사용할 수 있게 된다.**

#### 2. 열거 타입을 이용한 싱글턴

*   **열거 타입**: 싱글턴을 구현하는 세 번째 방법으로, 원소가 하나인 열거 타입을 사용하는 것입니다. 아래와 같이 선언할 수 있습니다.

    ```java
    public enum Elvis {    
        INSTANCE;  
              
        public void leaveTheBuilding() {        
        // 메서드 구현    
    }}
    ```
* **장점**:
  * **간결함**: 코드가 더 간단하고 이해하기 쉬워짐
  * **직렬화**: 추가적인 노력 없이 자동으로 직렬화가 가능하며, 복잡한 직렬화 상황에서도 안전
  * **리플렉션 공격 방지**: 리플렉션을 통해 새로운 인스턴스가 생성되는 것을 방지하며, 열거 타입은 자바에서 싱글턴 패턴을 안전하게 구현하는 방법으로 널리 권된다.
* **제한사항**: 열거 타입은 다른 클래스를 상속할 수 없기 때문에, 싱글턴이 다른 클래스를 상속해야 할 경우에는 사용할 수 없다. 그러나 열거 타입은 다른 인터페이스를 구현할 수 있다.

#### 결론

결론적으로, `readResolve` 메서드를 사용하여 직렬화 시 진짜 인스턴스를 반환함으로써 싱글턴을 보장하고, 원소가 하나인 열거 타입을 사용하여 간결하고 안전하게 싱글턴을 구현하는 방법이 있다. 이 두 가지 방법은 각각의 상황에 맞게 사용할 수 있으며, **특히 열거 타입은 복잡한 직렬화와 리플렉션 공격에 대한 안전성을 제공하는 최선의 선택으로 여겨진다.**

