# messageConverter가 무엇인가요?

## 스프링이란?

> 🔗 강의링크 : [https://www.inflearn.com/course/스프링부트-개념정리/dashboard](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%EA%B0%9C%EB%85%90%EC%A0%95%EB%A6%AC/dashboard)

### 1. 스프링은 MessageConverter를 가지고 있다. 기본값은 현재 Json이다.

예를 들어 영어권에 사람과 한국사람이 대화를 할 때 다른 언어로 대화 시 보내는 사람이 번역을 해서 보내거나 받는 사람이 번역을 해야한다. 이렇게 되면 불편하다.

그래서 한국도 영어사람도 이해할 수 있는 중간언어를 만들게 되는데.. 이것처럼 **중간 데이터**를 만들게 된다. **중간 데이터**란 원래 초기에는 `xml`을 많이 사용했지만 이제는 `JSON`을 많이 쓰는 추세이다.

#### 프로그래밍 언어로 예시를 들어보자

프로그래밍 언어로 예시를 들자면 **자바 object를 던지게 되면 파이썬 object는 이해할 수 없다.** 왜냐하면 자바의 object와 파이썬의 object는 서로 다르기 때문이다.

![](https://velog.velcdn.com/images/prettylee620/post/ff12462c-a4f3-407d-94bd-868306091dc2/image.png)

그렇기 때문에 자바 object **전송되기 전에** 중간 언어인 `JSON`으로 바꿔져서 파이썬이 받게 된 후 다시 파이썬 object로 바꾼다.. `JSON`은 **모든 언어가 이해할 수 있는 중간 언어이기 때문**이다.

![](https://velog.velcdn.com/images/prettylee620/post/2d8ae6f5-88ac-47cb-94d9-51cc5c542d39/image.png)

**자바**

```java
class Animal{
	int num = 10;
	String name = "사자";

}
```

**Json**

```java
{"num":10, "name":"사자"}
```

#### 🤔 그렇다면 messageConverter란 뭔데..?

자바 object를 JSON 형태로 개발자가 바꿀 필요 없이 바꿔주는 것을 `MessageConverter`이라고 한다.\\

![](https://velog.velcdn.com/images/prettylee620/post/97fe853d-7320-4fb2-95ec-6def6721ff50/image.png)

요청 시와 응답 시 모두 MessageConvertor가 바꿔주는 것으로 MessageConvertor은 **spring에 라이브러리로 존재하며 jackson이라고 불린다.**

![](https://velog.velcdn.com/images/prettylee620/post/8ff15f06-5c0f-4933-b17b-491c7f8e82c9/image.png)

### 2. 스프링은 BufferedReader와 BufferedWriter를 쉽게 사용할 수 있다.

통신의 경우 전기선(전류) 처럼 주르륵 흐르게 되어 있으며, `bit 단위`로 0, 1, 0, 1, 1, 1, 0 이런식으로 표현하게 되어 있다.

통신의 경우 영어권에서 먼저 발전하게 되었는데.. bit 단위로 할려니 불편해서 영어 한문자로 어떻게 표현하지? 🤔 라고 생각해보니 총 8bit = $2^8$가 필요하며, 256가지의 문자전송가능하다. 참고로 한글은 16bit 필요하다.

그래서 8bit씩 끊어 읽어 그렇다면 한 문자씩 받을 수 있을 거야라고 해서 **8bit = 1byte**라고 한다. `1byte`는 **통신의 최소단위**로 본다.

> 1byte = 하나의 문자

한국은 2Byte로 인코딩하고 영어권은 1Byte로 통신하게 되면.. 데이터 통신이 불가능 하게 된다. 그렇기 때문에 그래서 **유니코드에서 정해둔 것**이 `UTF-8`이라는 인코딩을 제공해주는 데 **3Byte 통신 방식**이다.

![](https://velog.velcdn.com/images/prettylee620/post/239f555d-e9be-4f31-b20e-156f58e17b51/image.png)

자바는 데이터를 읽을 때 `InputStream`으로 읽는 데 바이트로 읽는다. 바이트는 문자가 아니기 때문에 처리할 때 복잡해진다. 문자로 (char) 캐스팅해서 처리해야 한다는데.. 복잡하다.

![](https://velog.velcdn.com/images/prettylee620/post/8bdfd6ce-2f0f-455f-957c-06cb4d9a5a55/image.png)

그래서 첫 대안이 `InputStramReader`는 문자 하나를 받는다. 근데 이거 또한 배열에 경우 여러 개의 문자를 써서 받아야 해서 **최대 크기를 정해둬야 하는데.. 이렇게 되면 작은 크기를 받을 때 낭비가 심해서 사용하지 않는다.**

> `BufferedReader`를 사용하게 되면 **가변길이의 문자를 받을 수 있다. request.getReader()**

데이터를 쓸 때도 `BufferedWriter`를 써야 하는데 내려쓰기 기능이 없어서 `PrintWriter`를 쓰며, 함수의 경우 **print(), println()** 제공 해준다.

![](https://velog.velcdn.com/images/prettylee620/post/2aa84794-e95b-4027-afc8-82535f78af50/image.png)

> 즉, `BufferedWriter`란 Byte Stream을 통해 데이터를 전송할 때 **전송 단위가 문자열로 가변 길이의 데이터를 쓰게 해주는 클래스**이다.

이것들을 직접 구현할 필요없고 어노테이션을 제공 `@ResponseBady`⇒ BufferedWrite가 동작, 데이터를 받을 때는 `@RequestBody` ⇒ BufferedReader

### 3. 스프링은 계속 발전중이다.
