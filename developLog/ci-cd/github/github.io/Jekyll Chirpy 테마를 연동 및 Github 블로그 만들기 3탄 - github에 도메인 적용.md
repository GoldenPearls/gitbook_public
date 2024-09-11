# Jekyll Chirpy 테마를 연동 및 Github 블로그 만들기 3탄 - github에 도메인 적용

## 도메인 적용

### 1. 자신이 산 사이트에서 github랑 dns 연동해줘야 함(가비아기준)

도메인 구매 후!!

> * My 가비아 > dns 관리 툴에서 아래 와 같이 설정해줌

<figure><img src="../../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

나의 커스텀 도메인은 앞에 **www 가 없는 APEX 도메인 형태**라고 한다. [이글](https://medium.com/@stacyyya/github-pages%EC%97%90-custom-domain-%EC%97%B0%EA%B2%B0%ED%95%98%EA%B8%B0-feat-godaddy-79945b3ac5a0)에서 보게 됨

\
APEX 도메인은 A 타입으로 총 4개의 IP 주소를 추가해주어야 한다.

* type = A
* host = @
* points to (value) = IP 주소

```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

만약 dns 연결이 안되면, [글](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site) 참조

### 2. github에서 적용시키기&#x20;

<figure><img src="../../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

> **<< 도메인  적용 >>**
>
> * 자신의 도메인 연결할 리포지토리로 들어감
> * `⚙️ Settings` 클릭
> * Github pages에서 branch main root로 두고 custom domin에 올림
> * save 를 누르고 아래의 사진처럼 뜨면 연결 성공(좀 시간 걸림)

<figure><img src="../../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

그리고 CNAME 이 추가됐을 거임!

<figure><img src="../../../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

이제 잘 바뀌어서 뜸!!

<figure><img src="../../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>
