# Jekyll Chirpy 테마를 연동 및 Github 블로그 만들기 2탄 - fork한 것 mirror 해오기

## fork한 것 리포지토리 이동

### 1. **Github PAT 발급**

앞에서 테스트 해보면 불편한 게 fork 한 것이다 보니 commit의 형식 제한 되어 있고 해서 불편함 뿐만 아니라 제약이 너무 걸림



그렇기 때문에 commit 기록을 그대로 가지고 오는 `mirror`를 사용하기로 함 다만, fork 한 것 을 그렇게 가지고 오기 위해서는 **자신의 토큰**을 사용해줘야 한다.



> **<< Github PAT 발급 >>**
>
> * &#x20;Github에서 로그인 후 우측 상단의 프로필 아이콘 클릭
> * `⚙️ Settings` 클릭
> * 좌측 메뉴 최하단에 `<> Developer Settings` 클릭
> * `Personal access tokens` 펼쳐서 `Tokens (classic)` 클릭
> * `Generate new token (classic)` 클릭
> * select scope에서 자신이 필요한 권한 체크
> * 토큰의 경우 한 번만 보여주니 따로 잘 기록해둘 것!

✅ 토큰 생성시 고려할 각 항목

* `Note`는 본인이 토큰에 부여할 이름,
* `Expiration`은 토큰 만료 기한,
* `Select scopes`는 해당 토큰에 줄 권한 - 보통 repo, workflow를 많이 사용하는 듯

<figure><img src="../../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

### 2. mirror 사용해서 리포지토리 복사해 넣기

> 그동안의 커밋기록 또한 복사되니 걱정 nono

> 참고 : [https://soranhan.tistory.com/11](https://soranhan.tistory.com/11)

1. **일단 내 github에 새로운 레파지토리를 만든다.**
2. **terminal을 연다.**
3. **복사하고자 하는 repository를 bare clone한다.**

```
$ git clone --bare https://github.com/exampleuser/old-repository.git
```

4. **새로운 레파지토리로 Mirror-push**

> Token 부분에 자신이 기록한 것 넣기!

```
$ cd old-repository.git
$ git push --mirror https://<TOKEN>@github.com/exampleuser/new-reposit
```

5. **처음에 임시로 생성했던 local repository를 삭제**

```
$ cd ..
$ rm -rf old-repository.git
```

<figure><img src="../../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

커밋 기록까지 복사 된다!!



##
