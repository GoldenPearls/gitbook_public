---
description: 러스트 스터디 6주차 발표를 하기 위해 정리한 결과와 패닉 파트
---

# Chater 06. 오류 처리

> [노션 정리](https://swits.notion.site/WEEK-6-ec11487900754d42828b2b978359cb02?pvs=4) : 좀 더 색깔이나 덧글로 모르는 단어까지 걸어두고 조금 더 정갈하게 정리해뒀다. 발표를 위한 발제글 올리기

This chapter covers the two different kinds of error handling in Rust: panic and Results.

> Rust에서의 두 가지 에러 처리 방법인 `패닉(panic)`과 `결과(Result)`를 다룬다.

Ordinary errors are handled using the Result type. Results typically represent problems caused by things outside the program, like erroneous input, a network outage, or a permissions problem. That such situations occur is not up to us; even a bug-free program will encounter them from time to tim

일반적인 에러는 **Result 타입**을 사용하여 처리한다. `Result`는 보통 프로그램 외부의 요인, 예를 들어 잘못된 입력, 네트워크 장애, 또는 권한 문제로 인해 발생하는 문제를 나타낸다. 이러한 상황은 우리가 통제할 수 없으며, 버그가 없는 프로그램이라도 가끔은 이런 문제를 마주하게 된다.

Panic is for the other kind of error, the kind that should never happen.

> **패닉**은 다른 종류의 오류, 즉 절대 발생해서는 안 되는 에러입니다.

### 01. Panic(패닉)

A program panics when it encounters something so messed up that there must be a bug in the program itself.

프로그램 자체에 있는 버그로 인해 문제가 생기면 프로그램은 패닉에 빠진다.

* Out-of-bounds array access
* Integer division by zero
* Calling .expect() on a Result that happens to be Err
* Assertion failure
* 배열의 범위 밖에 있는 요소에 접근하는 행위
* 정수를 0으로 나누는 행위
* Err이 되어버린 Result에 대고 .expect()를 호출하는 행위
* 단언문 실패

What these conditions have in common is that they are all-not to put too fine a point on it--the programmer's fault. A good rule of thumb is: "Don't panic."

위의 조건들의 **공통점**은 모두 프로그래머가 저지르는 실수라는 것으로 당황스럽다고 해도 패닉에 빠지지는 말자.

#### 패닉 매크로

(There's also the macro panic! (), for cases where your own code discovers that it has gone wrong, and you therefore need to trigger a panic directly. panic!() accepts optional println! ()-style arguments, for building an error message.)

내가 작성한 코드가 잘못된 길로 들어서서 스스로 패닉에 빠져나가야 할 때 쓸 수 있는 `panic!()`이란 매크로도 있다. panic!()은 오류 메세지를 작성을 위한 println!() 스타일의인수를 옵션으로 받는다.

> 위의 뜻에 경우, 즉, 코드를 작성하다 예상치 못한 오류가 발생하면, 프로그램이 계속해서 실행되면 안 되는 상황이 있는데 이럴 때는 프로그램이 스스로 중단되도록 하는 것이 안전하다. 이를 **패닉에 빠져나간다고 표현**한다.

1. **panic!() 매크로**

* 그때 rust에서는 이런 치명적인 오류가 발생시 panic!() 매크로 사용 가능, 이 매크로를 호출하면 프로그램 즉시 중단
* 즉, `panic!()`은 코드가 "더 이상 진행할 수 없는 상황"에 도달했을 때 사용

2. **오류 메세지 작성**

* `panic!()` 매크로는 오류가 발생했을 때, 왜 프로그램이 중단되었는지를 알려주는 메시지를 출력할 수 있다.
* 이 메시지는 `println!()` 매크로와 비슷하게 작성할 수 있습니다. 예를 들어, 변수의 값을 포함하는 형태로 메시지를 구성할 수 있다.

```rust
fn main() {
    let value = -1;
    if value < 0 {
        panic!("Value {} is negative, which is not allowed!", value);
    }
}

```

* 여기서 `value`가 음수인지 확인
* 만약 `value`가 음수라면, `panic!()`을 호출하여 프로그램을 중단
* `panic!()`은 "Value -1 is negative, which is not allowed!"와 같은 오류 메시지를 출력
* 이 메시지는 `println!()` 매크로처럼, `{}`를 사용하여 `value`의 값을 포함할 수 있다.

But we all make mistakes. When these errors that shouldn't happen do happen-what then? Remarkably, Rust gives you a choice. Rust can either unwind the stack when a panic happens or abort the process. Unwinding is the default. 하지만 우리 모두는 실수를 하고 발생해서 안되는 오류들이 발생하면, 러스트는 우리에게 선택의 기회를준다. 러스트는 패닉이 발생하면 스택을 해제하거나 프로세스를 중단할 수 있다. 기본 동작은 **해제**이다.

### 02. Unwinding(해제)

hen pirates divvy up the booty from a raid, the captain gets half of the loot. Ordinary crew members earn equal shares of the other half. (Pirates hate fractions, so if either division does not come out even, the result is rounded down, with the remainder going to the ship's parrot.)

해적들이 약탈한 전리품을 나눌 때, 선장은 전리품의 절반을 가져가고, 일반 선원들은 나머지 절반을 동등하게 나눈다. (해적들은 분수를 싫어하기 때문에 나눗셈이 딱 맞지 않으면 결과는 내림하고 나머지는 선박의 앵무새에게 돌아간다.)

```rust
fn pirate_share(total: u64, crew_size: usize) -> u64 {
    let half = total / 2;
    half / crew_size as u64
}
```

This may workfine for centuries until one day it transpires that the captain is the sole survivor of a raid. If we pass a crew\_size of zero to this function, it will divide by zero. In C++, this would be undefined behavior. In Rust, it triggers a panic, which typically proceeds as follows:

이 코드는 몇 세기 동안 잘 작동하다가 어느 날 선장이 유일한 생존자라는 사실이 밝혀질 수 있습니다. 이 함수에 `crew_size`를 0으로 전달하면 0으로 나누게 됩니다. C++에서는 이것이 미정의 동작을, **Rust**에서는 `패닉`을 발생시킵니다.

#### 러스트가 패닉에 빠지고 Unwinding하는 과정

1. 오류 메세지

If you set the RUST\_BACKTRACE environment variable, as the messages suggests, Rust will also dump the stack at this point

![](https://velog.velcdn.com/images/prettylee620/post/19f10624-a2b4-4a81-be44-7dccde5ed0a0/image.png)

이 메세지는 제안하는 대로 RUST\_BACKTRACE 환경 변수를 설정하면 러스트가 해당 지점의 스택을 같이 덤프해준다.

덤프(dump) = 메모리의 내용을 자세히 보여주는 것을 의미

오류 메세지 대로 `RUST_BACKTRACE` 환경 변수를 설정하면, Rust 프로그램이 오류로 인해 중단될 때, **어디서 어떻게 중단되었는지 자세한 정보를 제공하는 스택 트레이스를** 출력한다. 이는 오류를 추적하고 디버깅하는 데 유용하다.

The stack is unwound. This is a lot like C++ exception handling.

2. 스택이 해제된다. 이는 C++의 **예외 처리**와 매우 유사하다.

The stack is unwound. This is a lot like C++ exception handling. Any temporary values, local variables, or arguments that the current function was using are dropped, in the reverse of the order they were created. Dropping a value simply means cleaning up after it: any Strings or Vecs the program was using are freed, any open Files are closed, and so on. User-defined drop methods are called too; see "Drop" on page 302.

* 현재 함수가 쓰던 임시 값, 지역 변수, 인수는 모두 **생성된 순서와 반대로 드롭**된다.

\= 프로그램이 `panic!`이나 오류로 인해 중단될 때, 프로그램의 각 함수 호출을 역순으로 정리하는

* 드롭 = 뒷정리 한다는 뜻
* 프로그램이 쓰던 String이나 Vec은 모두 해제되고 열린 File은 모두 닫힌다.

Once the current function call is cleaned up, we move on to its caller, dropping its variables and arguments the same way. Then we move to that function's caller, and so on up the stack

* 현재 함수 호출이 정리되면, 호출부로 이동해서 같은 방법으로 변수와 인수를 드롭한다. 그리고 스택 끝에 닾을 때까지 이런식으로 계속해서 그 함수의 호출부로 이동해 정리한다.

즉, 위의 것을 풀어 설명하면,

1. 현재 함수호출이 정리되면,

* 프로그램이 중단이 되었을때, 먼저 중단된 시점에 실행 중이던 현재 함수가 있다.
* rust는 이 함수에서 사용 중인 모든 변수와 리소스를 정리(메모리 해제, 파일닫기 등 )한다.

2. **호출부로 이동해서 같은 방법으로 변수와 인수를 드롭한다**:

* 현재 함수가 정리된 후, Rust는 이 함수를 호출한 **상위 함수**로 이동
* 그리고 상위 함수에서도 동일하게 변수와 리소스를 정리

3. **스택 끝에 닿을 때까지**:
   * Rust는 이러한 과정을 계속해서 반복하여 함수 호출 스택의 끝, 즉 프로그램이 시작된 지점까지 이동하며 모든 함수를 정리
   * 프로그램이 처음 시작된 `main` 함수까지 모든 함수가 역순으로 정리될 때까지 이 작업 수행

Perhaps panic is a misleading name for this orderly process. A panic is not a crash. It's not undefined behavior. It's more like a RuntimeException in Java or a std::logic\_error in C++. The behavior is well-defined; it just shouldn't be happening

패닉은 이 규칙적인 과정에 어울리지 않는 이름일지 모른다. 미정의 동작보다는 자바의 `RuntimeException`과 가깝다. 단지 발생하면 안 될 뿐이다.

Finally, the thread exits. If the panicking thread was the main thread, then the whole process exits (with a nonzero exit code).

4. 끝으로 스레드가 종료된다. 만일 패닉에 빠진 스레드가 메인스레드였다면 (0이 아닌 종료 코드를 가지고) 전체 프로세스가 종료된다.

#### 패닉이라는 것..

The idea is that Rust catches the invalid array access, or whatever it is, before anything bad happens. It would be unsafe to proceed, so Rust unwinds the stack. But the rest of the process can continue running

러스트는 잘못된 배열 접근이든 뭐든 안 좋은 일이 **벌어지기 전**에 잡아낸다. = 안전성 패닉에 빠지면 계속 진행하는것이 위험하므로 러스트는 스택을 해제한다. 그러나 프로세스의 나머지 부분은 계속 실행을 이어갈 수 있다.

Panic is per thread. One thread can be panicking while other threads are going on about their normal business

패닉은 **스레드 별로 발생**한다. 한 스레드가 패닉에 빠져도 다른 스레는 정상적으로 일을 수행할 수 있다.

There is also a way to catch stack unwinding, allowing the thread to survive and continue running. The standard library function std::panic:: catch\_unwind() does this. We won't cover how to use it, but this is the mechanism used by Rust's test harness to recover when an assertion fails in a test

**스택 해제를 잡아서** 스레드를 죽지 않고 계속 실행되게 만드는 방법도 있다. 표준 라이브러리 함수 `std::panic::catch_unwind()`가하는 일이 바로 그것이다. 이 함수의 사용법은 다루지 않고 러스트의 테스트 도구가 **테스트에 있는 단언문 실패 시** 이 매커니즘을 써서 **원상 복구한다는 것**은 알아두기!

이 부분은 22장에서 자세히 다룬다고 함

Ideally, we would all have bug-free code that never panics. But nobody's perfect. You can use threads and catch\_unwind() to handle panic, making your program more robust.

절대로 패닉을 빠지지 않는 코드를 짤 수 있는 사람은 거의 없을 것 하지만, 스**레드와 catch\_unwind()로 패닉을 잘 다루면** 프로그램을 보다 견고하게 만들 수 있다.

One important caveat is that these tools only catch panics that unwind the stack. Not every panic proceeds this way.

> 꼭 알아둬야 할 점은 **스택을 해제하는 패닉만** 이런 식으로 잡을 수 있다는 것이다.

#### 자바의 try catch와 다른 점

* Rust에서 패닉은 일반적으로 **복구 불가능한 오류**로 간주됩니다. 따라서 패닉 후의 복구를 시도하는 대신 프로그램을 종료하는 것이 일반적입니다. `catch_unwind`을 사용해 패닉을 포착하는 것은 비상 상황에만 권장됩니다.
* Java의 예외는 **복구 가능한 오류**로 간주되며, 예외 처리 구문을 통해 정상적인 프로그램 흐름으로 복귀할 수 있습니다. 이는 Java가 예외를 프로그램 로직의 일부로 사용할 수 있게 합니다.

### 03. Aborting(중단)

Stack unwinding is the default panic behavior, but there are two circumstances in which Rust does not try to unwind the stack.

스택 해제는 패닉의 기본 동작이지만, 다음 두 가지 상황에서는 러스트가 **스택 해제를 시도하지 않는다.**

#### 첫 번째 상환

If a .drop() method triggers a second panic while Rust is still trying to clean up after the first, this is considered fatal. Rust stops unwinding and aborts the whole process.

만일 러스트가 첫 번째 패닉을 정리하고 있는 상황에서 `.drop()` 메서드가 **두 번째 패닉을 유발하면 이는 치명적인 상황으로 간주**하고, 러스트는 해제를 멈추고 전체 프로세스를 중단한다.

#### 두 번째 상황

Also, Rust's panic behavior is customizable. If you compile with -C panic-abort, the first panic in your program immediately aborts the process. (With this option, Rust does not need to know how to unwind the stack, so this can reduce the size of your compiled code.)

또한 러스트의 패닉 동작은 변경이 가능하다. 프로그램을 `-C panic=abort` 옵션으로 컴파일하면 **첫 번째** 패닉이 발생하는 즉시 프로세스가 중단된다(이 옵션을 쓰면 러스트가 스택 해제 방법을 몰라도 되기 때문에 컴파일된 코드의 크기가 줄어들 수 있다.)

It's unreasonable to expect every function in a program to anticipate and cope with bugs in its own code. Errors caused by other factors are another kettle of fish

프로그램에 있는 모든함수가자기 코드에 있는 버그를 예견해 대처할 수 있기를 기대하는 건 무리가 있다. 하지만 다른 요인에 의해 유발되는 오류는 전혀 다른 문제다.

### 04. Result

Rust doesn't have exceptions. Instead, functions that can fail have a return type that says so:

**러스트는 예외가 없다.** 대신 실패할 수 있는 함수가 다음과 같은 반환 타입을 갖는다.

```rust
fn get weather (location: LatLng) -> Result<Weather Report, io::Error>
```

The Result type in Rust indicates potential failure in functions. Rust에서 `Result` 타입은 함수 실행 시 발생할 수 있는 실패를 나타낸다.

When calling a function like get\_weather(), it returns either Ok(weather) for success or Err(error\_value) for an error, such as an io::Error. Rust requires handling these results to avoid compiler warnings. 예를 들어, `get_weather()` 함수를 호출하면 성공 시 **Ok(weather)**, 실패 시 \*\*Err(error\_value)\*\*를 반환하며, 이때 **error\_value**는 io::Error로 어떤 문제가 발생했는지 설명한다. Rust는 이러한 결과를 처리하도록 강제하며, `Result` 값을 사용하지 않으면 컴파일러 경고가 발생한다.

### 05. Catching Errors(오류 잡기)

```rust
match get weather (hometown) {
	Ok(report) => {
		display_weather (hometown, &report);
	}
	Err(err) => {
		println("error querying the weather: {}", err);
		schedule_weather_retry();
	}
}
```

> 다른 언어의 **try/catch**에 해당한다. 오류를 호출부에 넘기지 않고 직접 처리하고자 할 때 이 방법을 쓴다.

match is a bit verbose, so Result\<T, E> offers a variety of methods that are useful in particular common cases. Each of these methods has a match expression in its implementation. (For the full list of Result methods, consult the online documentation. The methods listed here are the ones we use the most.)

하지만, `match`는 코드를 구구절절 늘어놓는 경향이 있어 `Result<T, E>`는 **자주 겪는 몇 가지 상황에서 유용하게 쓸 수 있는 메서드들**을 모아 제공한다. 이 메서드들은 각자 자신의 구현안에 match 표현식을 가지고 있다.

#### 메서드 소개

1. **result.is\_ok(), result.is\_err() :** result가 성공 결과인지 오류 결과인지 말해 주는 bool을 반환한다.
2. **result.ok() :** 성공값이 있을 경우 그것을 Option로 반환한다. result가 `성공` 결과이면 Some (success\_value)를 반환하고, `그렇지 않으면` None을 반환하며 오륫값은 버린다.
3. **result.err() :** 오륫값이 있을 경우 그것을 Option로 반환한다.
4. **result.unwrap\_or(fallback) :** result가 **성공** 결과일 경우 성공값을 반환하고, **그렇지 않으면** fallback을 반환하며 오릇값은 버린다.

```rust
// 남부 캘리포니아의 평상시 예측치.
const THE_USUAL: WeatherReport= WeatherReport::Sunny (72);

// 실제 날씨 정보를 받아온다.
// 받아올 수 없으면 평상시 예측치를 대신 쓴다.
let report = get_weather(los_angeles).unwrap_or(THE_USUAL);
display_weather(los_angeles, &report);
```

이 메서드는 반환 타입이 Option가 아니라 이기 때문에\*\*.ok() 대신 쓰면 편리하다.\*\* 물론 적 절한 대체값이 있을 때만 쓸 수 있다.

1. **result.unwrap\_or\_else(fallback\_fn) :** 앞과 동일하지만 대체값을 직접 받는 게 아니라 **함수나 클로저를 받는다는 점이 다르다.** 쓰지도 않을 대체값을 계산하기 아까울 때 쓴다. `fallback_fn`은 오류 결과를 가질 때만 호출된다.

```rust
let report =
get_weather(hometown)
.unwrap_or_else(i_err! vague_prediction(hometown));

```

2. **result.unwrap() :** 마찬가지로 result가 성공 결과일 경우 성공값을 반환한다. **하지만 result가 오류 결과일 경우에는 패닉에 빠진다.** 이 메서드는 용도가 따로 있는데 그 부분은 잠시 뒤에 살펴본다.
3. **result.expect(message) :** unwrap()과 동일하지만 패닉에 빠졌을 때 출력할 메시지를 지정할 수 있다.

#### 끝으로 다음 메서드들은 Result에 있는 레퍼런스를 다룰 때 사용한다.

4. result.as\_ref() : Result\<T, E>를 Result<\&T, \&E>로 변환한다.
5. result.as\_mut() : 앞과 동일하지만 변경할 수 있는 레퍼런스를 빌려 온다는 점이 다르다. 반환 타입은 Result<\&mutT, \&mut E>다.

One reason these last two methods are useful is that all of the other methods listed here, except.is\_ok() and .is\_err(), consume the result they operate on.

마지막 두 메서드가 유용한 이유 중 하나는 .is\_ok() .is\_err()을 제외한 여기 나열된 다른 모든 메서드들이 작업 대상인 result를 소비(consume)한다는 것이다.

That is, they take the self argument by value. Sometimes it's quite handy to access data inside a result without destroying it, and this is what .as\_ref() and .as\_mut() do for us.

> 즉, 이들은 `self 인수`를 값으로 받는다. 경우에 따라서는 **result에 있는 데이터를 소멸시키지 않고 접근하는 것이 아주 유용할 때가 있는데,** 이렇게 할 수 있도록 해주는 것이 바로 `.as_ref()`와 `.as_mut()`다.

#### .as\_ref()와 .as\_mut()의 역할

* `.as_ref()`와 `.as_mut()` 메서드는 `Result` 타입에서 값을 참조로 접근하거나 가변 참조로 접근할 수 있도록 해주는 메서드
* `.as_ref()`는 값을 소멸시키지 않고, **참조로 접근**할 수 있게 한다. 이를 통해 값에 접근하되, 소유권을 넘기지 않으므로 원래 값은 여전히 유효하다.
* `.as_mut()`는 값을 소멸시키지 않고, **가변 참조로 접근**하여 값을 수정할 수 있게 한다.
* 이 메서드들은 원래 `Result` 값을 소유권을 넘기지 않고도 필요한 작업을 수행할 수 있게 해주는 유용한 도구디다.

```rust
fn main() {
    let result: Result<String, String> = Ok(String::from("Hello"));

    // result를 소멸시키지 않고, 내부의 값을 참조로 접근
    match result.as_ref() {
        Ok(value) => println!("Got a reference to: {}", value),
        Err(e) => println!("Got an error reference: {}", e),
    }

    // result는 여전히 사용할 수 있습니다.
    println!("Original result: {:?}", result);
}
```

For example, suppose you'd like to call result.ok(), but you need result to be left intact. You can write result.as\_ref().ok(), which merely borrows result, returning an Option<\&T> rather than an Option

예를 들어, result.ok()를 호출하더라도 **result가 그대로 남아 있길 원한다면** result.as\_ref().ok()라고 쓰면 된다. 이렇게하면 result를 빌려 오게 되므로 Option가 아니라 `Option<&T>`가 반환된다

### 06. Result Type Aliases(Result 타입 별칭)

Sometimes you'll see Rust documentation that seems to omit the error type of a Result:

러스트 문서를 보다 보면 가끔 오류 타입이 생략된 Result를 만날 때가 있다.

```rust
fn remove_file(path: &Path) -> Result<()>
```

이는 Result 타입 별칭을 쓰고 있다는 뜻이다.

A type alias is a kind of shorthand for type names. Modules often define a Result type alias to avoid having to repeat an error type that's used consistently by almost every function in the module. For example, the standard library's std::io module includes this line of code:

일종의 축약 표시로, 모듈은 보통 Result 타입 별칭을 정의해서 **그 모듈에 있는 함수들 대부분이 공통으로 사용하는 오류 타입을 반복해 적지 않아도 되게끔 만든다.**

This defines a public type std::io::Result. It's an alias for Result\<T, E>, but hardcodes std::io:: Error as the error type. In practical terms, this means that if you write use std::io;, then Rust will understand io::Result as shorthand for Result\<String, io::Error>

`use std::io;`라고 써 두면 러스트가 io::Result을 **Result\<String, io::Error>의 축약 표기**로 이해한다는 뜻이다.

이는 표준 라이브러리 모듈 안에 들어 있기 때문

### 07. Printing Errors(오류 출력하기)

> Rust에서 `std::error::Error` 트레이트는 다양한 오류 타입을 처리하기 위한 공통 인터페이스를 제공합니다. 이를 통해 오류를 출력하고, 근본 원인을 추적하며, 적절한 오류 메시지를 얻을 수 있다. `println!`과 `writeln!` 매크로를 사용하여 오류를 콘솔에 출력하거나 특정 스트림에 기록할 수 있다. `anyhow` 크레이트는 표준 라이브러리 이상의 강력한 오류 관리 기능을 제공한다.

Sometimes the only way to handle an error is by dumping it to the terminal and moving on.

경우에 따라서는 오류를 터미널에 덤프하고 다음으로 넘어가는 게 유일한 방법일 때가 있다.

```rust
println("error querying the weather: {}", err);
```

The standard library defines several error types with boring names: std::io:: Error, std::fmt:: Error, std::str::Utf8Error, and so on

표준 라이브러리는 std::io::Error, std::fmt::Error, std::str::Utf8Error 등 지루한 이름으로 된 오류 타입 몇 가지를 정의해 두고 있다.

All of them implement a common interface, the std::error:: Error trait, which means they share the following features and methods:

이들은 모두 공통 인터페이스인 `std::error:Error` 트레이트를 구현하고 있는데, 그 말인 즉슨 이들이 **아래의 기능과 메서드를 공유하고 있다는 뜻**이다.

1. **println!()**

All error types are printable using this. Printing an error with the {} format specifier typically displays only a brief error message. Alternatively, you can print with the {:?} format specifier, to get a Debug view of the error. This is less userfriendly, but includes extra technical information.

**모든 오류 타입은 이 메서드로 출력할 수 있다.** {} 형식 지정자로 오류를 출력하면 보통 간단한 오류만 표시된다. 이것 말고 `{:?}` 형식 지정자를 써서 해당 오류의 Debug 뷰를 보는 방법도 있는데 내용은 좀 딱딱해도 **추가적인 기술 정보를 같이 볼 수 있어서 좋다.**

![](https://velog.velcdn.com/images/prettylee620/post/2971e93c-dc2b-43c5-826d-0b89aea42cee/image.png)

2. err.to\_string() : 오류 메세지를 String으로 반환한다.
3. **err.source()**

> `source()` 메서드는 현재 오류의 근본 원인이 되는 이전 오류를 반환

Returns an option of the underlying error, if any, that caused err

* err의 원인이 되는 오류가 있을 경우, 그것을 Option으로 반환한다.

For example, a network error might cause a banking transaction to fail, leading to your boat being repossessed. Here, err.to\_string() might be "boat was repossessed", and err.source() would return the error related to the failed transaction. This transaction error’s to\_string() could be "failed to transfer $300 to United Yacht Supply", and its source() might return a detailed io::Error about the specific network outage.

* 예를 들어, 네트워크 오류로 인해 은행 거래가 실패하고, 그로 인해 보트가 압류되었다고 가정합시다. 이 경우, `err.to_string()`은 "boat was repossessed"(보트가 압류됨)일 수 있으며, `err.source()`는 거래 실패와 관련된 오류를 반환할 것이다. 이 거래 오류의 `to_string()`은 `"failed to transfer $300 to United Yacht Supply"`(United Yacht Supply에 $300 이체 실패)일 수 있고, 이 오류의 `source()`는 네트워크 중단에 관한 **상세한 내용을 담은 io::Error를 반환**할 수 있다.

Since this third error is the root cause, its `.source()` method returns `None`. Standard library errors often return `None` for their source because they usually operate at a lower level. To print all available error information, use a function like the one provided.

* 이때 세 번째 오류는 문제의 근본 원인이므로, `.source(`)메서드는 None을 반환할 것이다. 표준 라이브러리의 오류는 보통 낮은 수준에서 작동하기 때문에, 원인을 반환하는 경우가 드물다. 오류에 관한 모든 정보를 출력하려면 제공된 함수를 사용해야 함

```rust
std::error::Error;
use std::io::{Write, stderr};
/// 오류 메시지를 'stderr`에 덤프한다.

/// 오류 메시지를 생성하는 도중이나 'stderr'에 기록하는 도중에 
/// 또 다른 오류가 발생하면 무시한다.
fn print_error(mut err: &dyn Error) {
	let_ = writeln!(stderr(), "error: {}", err);
	while let Some(source) = err.source() {
	let_ = writeln!(stderr(), "caused by: {}", source); 
	err = source;
	}
}
```

The writeln! macro works like println!, except that it writes the data to a stream of your choice. Here, we write the error messages to the standard error stream, std::io::stderr. We could use the eprintln! macro to do the same thing, but eprintln! panics if an error occurs. In print\_error, we want to ignore errors that arise while writing the message; we explain why in "Ignoring Errors" on page 169, later in the chapter.

rust의 **writeln!** 매크로는 \*\*println!\*\*과 유사하게 동작하지만, 데이터를 특정 스트림에 쓸 수 있게 해준다. 여기서는 오류 메시지를 `표준 오류 스트림(std::io::stderr)`에 쓰기 위해 \*\*writeln!\*\*을 사용한다. **eprintln!** 매크로도 stderr에 메시지를 쓸 수 있지만, 쓰는 도중 오류가 발생하면 패닉을 일으킨다. print\_error 함수에서는 메시지를 쓰는 동안 발생하는 오류를 무시하고 싶었는데, 이유는 오류 무시하기에서 나옴

#### writeln! 매크로

* **기능**: `writeln!` 매크로는 `println!`과 비슷하게 동작하지만, 주어진 스트림에 데이터를 씁니다. 이 스트림은 파일, 네트워크 소켓, 또는 표준 출력/오류 스트림일 수 있습니다.
* **사용 방법**: `writeln!`의 첫 번째 인수는 데이터를 쓸 대상 스트림입니다. 예를 들어, `stderr` 스트림에 쓰려면 `writeln!(std::io::stderr(), "error: {}", err)`와 같이 사용합니다.
* **유연성**: 이는 데이터를 표준 출력 대신 다른 위치로 보내고자 할 때 유용합니다.

```rust
use std::io::{self, Write};

fn main() {
    let mut handle = io::stdout(); // 표준 출력에 쓰기
    writeln!(handle, "Hello, world!").unwrap();

    let mut handle = io::stderr(); // 표준 오류 스트림에 쓰기
    writeln!(handle, "An error occurred!").unwrap();
}

```

#### eprintln! 매크로

* **기능**: `eprintln!` 매크로는 `println!`과 유사하게 작동하지만, 메시지를 표준 오류 스트림(`stderr`)에 씁니다.
* **패닉 발생**: `eprintln!`은 메시지를 쓰는 도중 오류가 발생하면 패닉을 일으킵니다. 이는 추가적인 오류 처리를 필요로 할 수 있습니다.

```rust
fn main() {
    eprintln!("This is an error message!");
}

```

#### `print_error` 함수에서 `writeln!`을 사용하는 이유

* **오류 무시**: `print_error` 함수에서는 오류 메시지를 쓰는 도중에 발생하는 오류를 무시하고자 합니다. 이는 추가적인 복잡성을 피하기 위해서입니다.
* **안정성**: `writeln!`을 사용하면, 쓰기 작업 중 발생하는 오류를 `Result`로 반환하고, 이를 무시할 수 있는 유연성을 제공합니다. 반면 `eprintln!`은 패닉을 발생시켜 프로그램의 흐름을 방해할 수 있습니다.
* **의도된 동작**: `print_error` 함수는 이미 발생한 오류를 보고하는 도중에 또 다른 오류가 발생하더라도 프로그램의 주 실행 흐름을 방해하지 않도록 설계되었습니다.

```rust
use std::error::Error;
use std::io::{self, Write};

fn print_error(mut err: &dyn Error) {
    let _ = writeln!(io::stderr(), "error: {}", err);
    while let Some(source) = err.source() {
        let _ = writeln!(io::stderr(), "caused by: {}", source);
        err = source;
    }
}
```

* 이 함수에서는 `writeln!`을 사용하여 `stderr`에 오류 메시지를 씁니다.
* 쓰기 작업 중 발생할 수 있는 오류를 무시하기 위해 `Result`를 처리하지 않고, 단순히 `_`로 무시합니다.

The standard library's error types do not include a stack trace, but the popular anyhow crate provides a ready-made error type that does, when used with an unstable version of the Rust compiler

**표준 라이브러리의 오류 타입은 스택 트레이스를 포함하지 않는다.** 스택 트레이스를 포함하는 오류 타입이 필요할 때는 `anyhow` 크레이트의 도움을 받으면 되는데, 단 이 경우에는 정식으로 릴리스되지 않은 불안정한 버전의 러스트 컴파일러를 써야 한다.

#### anyhow

* `anyhow`는 Rust의 오류 처리에 강력한 기능을 추가하는 크레이트입니다. 복잡한 오류 체인과 상세한 스택 트레이스를 관리할 수 있는 기능을 제공합니다. Rust의 불안정(Unstable) 버전에서 제공되는 특정 기능을 활용하여, 더 많은 디버깅 정보를 제공합니다.
* **불안정한 Rust 컴파일러(Unstable Rust Compiler)**:
  * Rust는 주기적으로 새로운 기능을 실험하기 위해 "불안정한" 버전을 출시합니다.
  * `anyhow` 크레이트는 이러한 불안정 버전에서만 사용 가능한 특정 기능을 활용하여, 스택 트레이스와 같은 추가적인 오류 정보를 제공합니다.

#### `anyhow` 사용 예시

```rust
use anyhow::{Result, Context};

fn main() -> Result<()> {
    let file_content = std::fs::read_to_string("non_existent_file.txt")
        .context("Failed to read the file")?;
    println!("{}", file_content);
    Ok(())
}

```

* `anyhow::Context` 트레이트를 사용하여 오류 메시지에 추가적인 정보를 제공할 수 있습니다.
* `context("Failed to read the file")`는 기본 오류 메시지에 더 자세한 문맥을 추가하여, 디버깅을 쉽게 합니다.

### 08. Propagating Errors(오류 전파하기)

It is simply too much code to use a 10-line match statement every place where something could go wrong.

문제가 생길 수 있는 곳마다 10줄짜리 match문으로 코드를 도배하다시피 하는 건 너무 과하다.

Instead, if an error occurs, we usually want to let our caller deal with it. We want errors to propagate up the call stack

오류가 발생하면 보통은 호출부에 처리를 맡기고 싶어 한다. 오류가 호출 스택을 타고 \*\*전파(propagation)\*\*되길 원하는 것이다.

Rust has a ? operator that does this. You can add a ? to any expression that produces a Result, such as the result of a function call:

러스트에는 이런 일을 하는 `?`연산자가 있다. `?`는 **함수 호출 결과와 같이 Result를 산출하는 모든 표현식에 붙여 쓸 수 있다.**

```rust
let weather = get_weather(hometown)?;

```

The behavior of? depends on whether this function returns a success result or an error result:

앞의 코드에서 `?`의 동작은 **함수가 성공 결과를 반환하는지, 오류 결과를 반환하는지**에 따라 달라진다.

> Rust에서 `?` 연산자는 `Result` 또는 `Option`을 반환하는 함수에서 오류 처리를 간소화합니다.

* On success, it unwraps the Result to get the success value inside. The type of weather here is not Result\<Weather Report, io::Error> but simply WeatherReport.
* `성공`한 경우에는 Result를 풀어서 그 안에 있는 **성공값을 꺼낸다.** 여기서 **weather**의 타입은 Result\<WeatherReport, io::Error>가 아니라 그냥 `WeatherReport`다.
* On error, it immediately returns from the enclosing function, passing the error result up the call chain. To ensure that this works, ? can only be used on a Result in functions that have a Result return type.
* `오류`가 발생한 경우에는 즉시 바깥쪽 함수에서 복귀하고 오류 결과를 호출 체인 위로 전달한다. 이 때문에 `?`는 **반환 타입이 Result인 함수에서만** 쓸 수 있다.

There's nothing magical about the ? operator. You can express the same thing using a match expression, although it's much wordier:

match 표현식으로 가능하지만, ?으로 하면 간편하게 처리 가능하다는 것이다.

The only differences between this and the ? operator are some fine points involving types and conversions. We'll cover those details in the next section. In older code, you may see the try! () macro, which was the usual way to propagate errors until the? operator was introduced in Rust 1.13

다만, 이 둘은 타입과 변환이 개입되는 부분에서 약간의 미묘한 차이점이 있다. 오래된 코드를 읽다 보면 try!() 매크로를 보게 될 수도 있다. `?` 연산자가 도입되기 전까지는 대부분 **이 매크로를 써서 오류를 전파**했다.

```rust
 let weather try! (get_weather (hometown));
```

The macro expands to a match expression, like the one earlier. It's easy to forget just how pervasive the possibility of errors is in a program, particularly in code that interfaces with the operating system. The ? operator sometimes shows up on almost every line of a function:

이 매크로는 이전 예시처럼 `match` 표현식으로 확장된다. 프로그램에서 오류가 발생할 가능성을 쉽게 잊을 수 있지만, 특히 운영 체제와 인터페이스하는 코드에서는 그렇다. `?` 연산자는 때로 함수의 **거의 모든 줄에서 등장할 수 있다:**

```rust
use std::fs;
use std::io;
use std::path::Path;

fn move_all(src: &Path, dst: &Path) -> io::Result<()> {
    for entry_result in src.read_dir()? { // 디렉터리를 여는 도중 오류가 발생할 수 있습니다.
        let entry = entry_result?; // 디렉터리를 읽는 도중 오류가 발생할 수 있습니다.
        let dst_file = dst.join(entry.file_name());
        fs::rename(entry.path(), dst_file)?; // 파일 이름 변경 중에 오류가 발생할 수 있습니다.
    }
    Ok(()) // 휴!
}

```

`?` 연산자는 `Option` 타입에서도 비슷하게 작동한다. `Option`을 반환하는 함수에서는, `?`를 사용하여 값을 언랩하고 `None`인 경우(값이 존재하지 않는 경우) 조기에 반환할 수 있다:

```rust
let weather = get_weather(hometown).ok()?;
```

이 코드는 두 가지 주요 작업을 수행합니다:

1. `get_weather(hometown)` 함수 호출의 결과를 `ok()` 메서드로 변환합니다.
2. `ok()` 메서드의 결과에 `?` 연산자를 사용합니다.

#### 각 부분의 역할

**1. get\_weather(hometown)**

* get\_weather는 `hometown`이라는 인자를 받아 호출되는 함수입니다.
* 이 함수가 반환하는 타입은 `Result<T, E>`입니다. 여기서 `T`는 **성공적으로 반환되는 값의 타입**이고, `E`는 **오류가 발생했을 때 반환되는 오류 타입**입니다.

**2. .ok()**

* ok() 메서드는 Result\<T, E> 타입에서 사용됩니다.
* ok() 메서드는 Result를 Option으로 변환합니다:
  * 만약 Result가 Ok(value)라면, Some(value)를 반환합니다.
  * 만약 Result가 Err(error)라면, None을 반환합니다.

즉, get\_weather(hometown).ok()는 Result\<T, E>를 Option로 변환합니다.

**3. ? 연산자**

* ? 연산자는 Option 타입이나 Result 타입에서 사용되어, 값이 있으면 그 값을 반환하고, 없으면 함수 전체를 조기에 종료합니다.
* Option에서 ?를 사용하면:
  * 값이 \*\*Some(value)\*\*이면 값을 반환합니다.
  * 값이 `None`이면, 호출한 함수가 즉시 None을 반환합니다. 즉시. 종료한다.

**Option 타입으로 변환된 이유**

위의 과정을 통해, get\_weather(hometown)가 반환하는 **Result 타입의 값을 Option 타입으로 변환한 후 ? 연산자를 사용하고 있습니다**. 따라서 최종적으로 weather는 Option 타입이 됩니다.

즉, `let weather = get_weather(hometown).ok()?;`는 `weather`를 `Option` 타입으로 변환하고, 값이 없으면 조기에 반환하여 함수의 흐름을 제어합니다.

### 09. Working with Multiple Error Types(여러 오류 타입 다루기)

```rust
use std::io::{self, BufRead};
		/// 텍스트 파일에서 정수들을 읽어 온다.
		/// 파일에는 숫자가 한 줄에 하나씩 있다고 가정한다.
		fn read_numbers(file: &mut dyn BufRead) → Result<Vec<i64>, io::Error>{
			let mut numbers = vec![];
			for line_result in file. lines() {
				// 줄을 읽을 때 실패할 수 있다.
				numbers.push(line.parse()?); // 정수를 파싱할 때 실패할 수 있다.
				let line = line_result?;
		}
		Ok(numbers)
}
```

Rust gives us a compiler error:

러스트는 앞의 코드에 대해 다음과 같은 컴파일러 오류를 낸다.

![](https://velog.velcdn.com/images/prettylee620/post/50ddfa32-9319-46d7-9fd5-dce616768466/image.png)

![](https://velog.velcdn.com/images/prettylee620/post/23887057-22df-471a-bc4d-bbf2f9049fe4/image.png)

The terms in this error message will make more sense when we reach Chapter 11,

여기 나오는 오류 메세지의 용어들은 트레이트를 다루는 11장을 읽고 나면 이해가 될 것이라고 함

which covers traits. For now, just note that Rust is complaining that the ? operator can't convert a std::num:: ParseIntError value to the type std::io::Error

`?`는 오류를 자동으로 전파하기 위해 변환이 필요한데, 현재 코드에서는 ? 연산자가 std::num::ParseIntError 값을 std::io::Error 타입으로 변환할 수 없어서 오류를 낸 것

참고로

**`std::convert::From` 트레이트**:

* Rust에서 한 타입을 다른 타입으로 변환하기 위해 사용하는 트레이트이다. `From` 트레이트가 구현된 두 타입 사이에서는 안전하게 변환할 수 있다.

There are several ways of dealing with this. For example, the image crate that we used in Chapter 2 to create image files of the Mandelbrot set defines its own error type, ImageError, and implements conversions from io:: Error and several other error types to ImageError. If you'd like to go this route, try the thiserror crate, which is designed to help you define good error types with just a few lines ofcode.

문제를 해결하는 방법은 여러가지가 있음

1. 2장에서 망델브로 집합에서 나온 **image 크레이트 사용**

A simpler approach is to use what's built into Rust. All of the standard library error types can be converted to the type Box\<dyn std::error:: Error + Send + Sync + 'static>. This is a bit of a mouthful, but dyn std::error::

1. 좀 더 간단한 방법은 **러스트의 내장된 기능** 이용

* 표준 라이브러리의 모든 오류 타입은 `Box<dyn std::error:: Error + Send + Sync+ 'static>` 타입으로 변환될 수 있다.

Error represents "any error," and Send + Sync + 'static makes it safe to pass between threads, which you'll often want. For convenience, you can define type aliases

* dyn std::error::Error는 '모든 오류'를 표현 하고, Send+ Sync+ 'static은 이를 스레드 간에 전달해도 안전하게 만들어 준다. 다음처럼 타입 별칭을 정의해 쓰면 편리하다.

```rust
type GenericError = Box<dyn std::error:: Error + Send + Sync + 'static>;
type GenericResult<T> Result<T, GenericError>;
```

Then, change the return type of read\_numbers() to GenericResult\<Vec>. With this change, the function compiles. The ? operator automatically converts either type oferror into a GenericError as needed.

그런 다음 \*\*read\_numbers()\*\*의 반환 타입을 \*\*GenericResult\<Vec>\*\*로 바꾼다. 이렇게 하고 나면 함수가 문제없이 컴파일된다. 이제? 연산자는 필요에 따라 두 오류 타입을 GenericError로 자동 변환한다.

Incidentally, the ? operator does this automatic conversion using a standard method that you can use yourself. To convert any error to the GenericError type, call GenericError::from():

여담이지만 연산자는 누구나 쓸 수 있는 표준 메서드를 써서 이 `자동 변환을 수행`한다. 임의의 오류를 GenericError 타입으로 변환하려면 **GenericError: :from()을 호출**하면 된다

```rust
let io_error = io::Error::new(
	io::ErrorKind::Other, "timed out");
	// io::Error를 만든다.
return Err(GenericError: :from(io_error)); // 직접 GenericError로 변환한다
```

The downside ofthe GenericError approach is that the return type no longer communicates precisely what kinds of errors the caller can expect. The caller must be ready for anything

`GenericError` 방식의 단점은 반환 타입이 이 이상 발생할 수 있는 **오류의 종류를 콕 찍어 알려 주지 않는다는 것**이다. 호출부는 만반의 준비가 되어 있어야 한다.

**GenericResult를 반환하는 함수를 호출할 때** 특정 유형으로 된 오류만 처리하고 나머지는 **그냥 전파하길 원한다면 제네릭 메서**드 `error.downcast_ref:: <ErrorType>()`을 쓰면 된다. 이 메서드는 만일 어떤 오류가 여러분이 찾는 그 특정 유형의 것일 경우 그 오류의 레퍼런스를 빌려 온다.

많은 언어가 이를 위한 문법을 내장하고 있지만, 실제로 쓰이는 일은 거의 드물다. 따라서 러스트는 이를 `메서드`로 처리한다.

### 10. '발생할 리 없는 오류 다루기’(Dealing with Errors That "Can't Happen”)

Example: Parsing a Configuration File

예시: 구성 파일 파싱

```rust
if next_char.is_digit(10) {
    let start = current_index;
    current_index = skip_digits(&line, current_index);
    let digits = &line[start..current_index];
    let num = digits.parse::<u64>();
}

```

**The Problem: str.parse::() Returns a Result**

문제: `str.parse::<u64>()`는 `Result`를 반환한다

```rust
"bleen".parse::<u64>() // ParseIntError: invalid digi
```

**Solution: Use `.unwrap()` When Confident**

해결책: 확실할 때는 `.unwrap()` 사용하기

```rust
let num = digits.parse::<u64>().unwrap();
```

Beware: Overflow Can Happen

주의: 오버플로가 발생할 수 있다

```rust
"99999999999999999999".parse::<u64>() // overflow error
```

Use `.unwrap()` or `.expect()` for Impossible Errors

불가능한 오류에 대해 `.unwrap()`이나 `.expect()` 사용하기

```rust
fn print_file_age(filename: &Path, last_modified: SystemTime) {
    let age = last_modified.elapsed().expect("system clock drift");
}
```

Error Handling with GenericError

`GenericError`를 사용한 오류 처리

The downside of the GenericError approach is that the return type no longer communicates precisely what kinds of errors the caller can expect. The caller must be ready for anything.

1. **Use .unwrap() and .expect() When Sure**:
   * If you are certain that a Result can only be Ok, use .unwrap() to get the value or .expect(message) to provide a custom panic message.
   * Result가 반드시 Ok일 것이라고 확신한다면, `.unwrap()`을 사용하여 값을 얻거나 `.expect(message)`를 사용하여 사용자 지정 패닉 메시지를 제공하세요.
2. **Drawbacks of GenericError**:
   * Using GenericError does not convey specific error types, requiring the caller to handle a wide range of potential errors.
   * GenericError를 사용하면 특정 오류 유형을 전달하지 않으므로, **호출부에서 다양한 잠재적 오류를 처리해야 합니다.**
3. **When to Use .unwrap() or .expect()**:
   * Use these methods when dealing with impossible errors or in scenarios where failing indicates a severe issue, and panic is appropriate.
   * 불가능한 오류를 처리하거나 실패가 심각한 문제를 나타내는 경우, 패닉이 적절한 상황에서는 이러한 `메서드`를 사용하세요.
4. **Handle Overflows and Unexpected Input**:
   * Even when confident, always consider edge cases like overflows or unexpected input, as these can lead to bugs if not properly handled.
   * 확실할 때에도 오버플로우나 예기치 않은 입력과 같은 엣지 케이스를 항상 고려하세요. 이를 제대로 처리하지 않으면 버그로 이어질 수 있습니다.

By understanding and appropriately using these techniques, you can handle errors efficiently in Rust, maintaining both code simplicity and robustness. 이러한 기술들을 이해하고 적절히 사용함으로써, Rust에서 오류를 효율적으로 처리하고, 코드의 간결성과 견고함을 유지할 수 있습니다.

GenericError 방식의 단점은 반환 타입이 이 이상 발생할 수 있는 오류의 종류를 콕 찍어 알려 주지 않는다는 것이다. 호출부는 만반의 준비가 되어 있어야 한다.

### 11. 오류 무시하기(Ignoring Error)

Sometimes we just want to ignore an error altogether.

가끔은 오류를 완전히 무시하고 싶을 때도 있다

```rust
writeln!(stderr(), "error: {}", err); // 경고: 사용하지 않은 결과
```

The idiom `let _ = ...` is used to silence this warning.

이럴 때는 `let _ = ...` 관용구를 쓰면 경고를 잠재울 수 있다.

```rust
let _ = writeln!(stderr(), "error: {}", err); // OK. 결과를 무시한다.
```

### 12. main()에서 오류 처리하기(Handling Errors in main())

However, you can also change the type signature so you can use ?:

오류 전파 단계가 너무 길어지면 결국 main()까지 오게 되므로 뭔가 조치를 취해야 한다. 보통 main()은 반환 타입이 Result가 아니라서 `?`를 쓸 수 없다.

```rust
fn main() {
	calculate_tides()?; // 오류: 더 이상 책임을 전가할 수 없다.
}
```

The simplest way to handle errors in `main()` is to use `.expect()`.

main()에서 오류를 처리하는 가장 간단한 방법은 `.expect()`를 쓰는 것이다.

```rust
calculate_tides().expect("error"); // 여기서 최종 책임을 진다.
```

The error message is a little intimidating, though:

단, 오류 메시지가 다소 난감하다

![](https://velog.velcdn.com/images/prettylee620/post/37c39580-d38d-4aa9-9eb4-b601e00ba6f3/image.png)

However, you can also change the type signature of main() to return a Result type, so you can use ?:

하지만 main()의 타입 시그니처를 바꿔서 **Result 타입을 반환하게 만들면** `?`를 쓸 수 있다.

```rust
fn main()> Result<(), TideCalcError>{
		let tides = calculate_tides();
		print_tides(tides);
		ok(())
	}
```

This works for any error type that can be printed with the {:?} formatter, which all standard error types, like std::io:: Error, can be. This technique is easy to use and gives a somewhat nicer error message, but it's not ideal

이 기법은 `{:?} 형식 지정자`로 출력할 수 있는 **모든 오류 타입에 대해 작동**한다. `std::io::Error`와 같은 표준 **오류 타입이 전부 여기에 해당**한다. 사용법이 간단하고 오류 메시지가 단순하다는 장점이 있지만, **그렇다고 완벽한 해결책이라고 할 수는 없다.**

If have more complex error types or want to include more details in your message, it pays to print the error message yourself:

다루어야 할 오류 타입이 복잡하거나 오류 메시지에 추가 정보를 넣고 싶을 때는 **차라리 직접 오류 메시지를 출력하는 게 더 낫다**

### 13. Declaring a Custom Error Type(사용자 정의 오류 타입 선언하기)

As with many aspects of the Rust language, crates exist to make error handling much easier and more concise. There is quite a variety, but one of the most used is thiserror, which does all of the previous work for you, allowing you to write errors like this:

러스트 언어의 많은 측면이 그렇듯 오류 처리도 외부 크레이트의 도움을 받으면 일이 훨씬 더 쉽고 간단해진다.

### 14. 왜 Result일까?

Rust requires the programmer to make some sort of decision, and record it in the code, at every point where an error could occur. This is good because otherwise it's easy to get error handling wrong through neglect.

* 러스트는 오류가 발생할 수 있는 모든위치에서 프로그래머가 모종의 결정을 내린 뒤 그것을 코드에 기록할 것을 요구한다. 이렇게 하면 오**류가 방치되어 잘못 처리되는 일이 줄기 때문에 좋다.**

The most common decision is to allow errors to propagate, and that's written with a single character, ?. Thus, error plumbing does not clutter up your codethe way it does in C and Go. Yet it's still visible: you can look at a chunk of code and see at a glance all places where errors are propagated

* 가장 일반적인 결정은 **오류가 전파**되도록 만드는 것인데, 여기에 필요한 코드는 `?` 한 문자뿐이다. 따라서 C와 고처럼 오류 배관작업으로 인해 코드가 어수선해지는 일이 없다. 게다다 가독성이 좋아서 코드를 조금를 봐도 오류가 전파되는 모든 것을 한눈에 파악할 수 있다.

Since the possibility of errors is part of every function's return type, it's clear which functions can fail and which can't. If you change a function to be fallible, you're changing its return type, so the compiler will make you update that function's downstream users.

* 오류의 가능성이 **모든 함수의 반환 타입에 명시**되기 때문에 실패할 수 없는 함수와 실패할 수 없는 함수를 명확히 구분할 수 있다. 실패할 수 없는 함수를 실패할 수 있는 함수로 바꾸는 일은 곧 `반환 타입`을 바꾸는 일이므로, 컴파일러가 함수의 사용처를 모두 업데이트할 수 있도록 도와 줄 것이다

Rust checks that Result values are used, so you can't accidentally let an error pass silently (a common mistake in C)

* 러스트는 Result 값의 사용 여부를 확인하기 때문에 **실수로 오류를 무시하고 넘어가는 일이 생길 수 없다**(C에서는 흔히 있는 실수다).

Since Result is a data type like any other, it's easy to store success and error results in the same collection. This makes it easy to model partial success. For example, ifyou're writing a program that loads millions of records from a textfile and you need a way to cope with the likely outcome that most will succeed, but some will fail, you can represent that situation in memory using a vector of Results

* Result는 평범한 데이터 타입이므로 성공 결과와 오류 결과를 같은 `컬렉션 안`에 담을 수 있는데, 이렇게 하면 부분적인 성공을 쉽게 모델링할 수 있다. 예를 들어, 텍스트 파일에서 수백만 개의 레코드를 읽어오는 프로그램을 작성 중이라고 하자. 이때 대부분은 성공하겠지만 간혹 실패할 수도 있는 상황에 대비할 방법이 필요하다면 Result 벡터로 해당 상황을 메모리에 표현할 수 있다.
