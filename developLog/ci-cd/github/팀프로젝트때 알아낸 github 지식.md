---
icon: person-chalkboard
---

# 팀프로젝트때 알아낸 github 지식

![](https://velog.velcdn.com/images/prettylee620/post/310494d3-2d3b-4810-bec1-853d7a06ef40/image.png)

## TMI

> 프로젝트를 하면서 팀 공통 템플릿을 만들면서 알게 된 github 사용법 등을 정리해봤다 그 이후 계속 추가예정이고 팀 프로젝트 끝나면 1부터 10까지 정리해야겠다

## 1. 리액트를 github에 올릴 때

1. 리액트와 스프링부트를 같이 연결한 것을 올리는데 **리액트 파일만 제외**가 됐다.

* 그래서 일부 파일만 올리는 것을 하려다가… 무수한 개행 문자 에러를 만나게 됨

2. `node/module`은 올리면 에러가 난다. 그렇기 때문에 제외 시켜줘야 함

* 제 1안 : 관련 프로젝트 파일 내 `.gitgnore`에 들어가서 수정

> 아무 위치에나 사진과 같이 추가 후 저장

![](https://velog.velcdn.com/images/prettylee620/post/fd2ae5cf-aa90-4268-93f3-7b2f9d9c8b83/image.png)

* 제 2안 : 명령어로 제거

![](https://velog.velcdn.com/images/prettylee620/post/5ee75d73-64a8-4bb9-ab8f-26f3cbb011a5/image.png)

3. 그 후 일부 파일만 올리기 위해 add

![](https://velog.velcdn.com/images/prettylee620/post/83b294c0-4114-418f-ae3c-a307e21c59c3/image.png)

4. warning이지만 큰 문제 없다 함

![](https://velog.velcdn.com/images/prettylee620/post/5bc2494c-0bd7-4655-b71b-5a16fa9ea0ee/image.png)

5. 그냥 무시하거나 이 명령어 쓰면 됨

![](https://velog.velcdn.com/images/prettylee620/post/2e091bec-a2dc-414a-a5b3-2f61effb0808/image.png)

6. 그 후 commit 하고 push

![](https://velog.velcdn.com/images/prettylee620/post/a32e8dbc-8876-4869-ba12-16e668f3fbc5/image.png)

![](https://velog.velcdn.com/images/prettylee620/post/c1e454c4-def1-4890-8aaf-85f81c58fb25/image.png)

7. 문제 없이 추가됨

![](https://velog.velcdn.com/images/prettylee620/post/d2b001dd-840c-4b13-a2c6-52ca505dee0d/image.png)

8. node-moudules의 경우 **`npm install` 패키지 설치**

* `node_modules/`는 Node.js 프로젝트에서 의존성 패키지들이 설치되는 디렉토리
* 이 디렉토리는 일반적으로 Git에 포함되지 않습니다. 왜냐하면 의존성 패키지들은 **`package.json`** 파일에서 관리되기 때문에, 다른 개발자나 서버에서 프로젝트를 클론한 후 **`npm install`** 또는 **`yarn install`** 명령을 통해 의존성 패키지들을 설치 가능

## 2. Git add시 경고 메세지

**`git add`** 명령을 실행할 때 나오는 경고 메시지는 여러 원인에 따라 발생할 수 있습니다. 대표적으로는 다음과 같은 경우들이 있습니다:

1. **대용량 파일**: Git은 대용량 파일(일반적으로 100MB 이상)에 대해서 경고 메시지를 보냅니다. 이러한 대용량 파일을 관리하기 위해서는 Git LFS를 사용하는 것이 좋습니다.
2. **개행 문자**: Windows와 Unix 계열 시스템(리눅스, 맥)은 파일의 끝에 사용하는 개행 문자가 다릅니다. 이로 인해 발생하는 문제를 경고하는 메시지가 나올 수 있습니다.
3. **.gitignore**: **`.gitignore`** 파일에 명시되어 있지 않고 추적되지 않은 파일들에 대한 경고가 나올 수 있습니다.

### 💫 해결 방법

1.  **경고 메시지를 숨김**: 경고 메시지가 너무 많고, 실제 문제가 되지 않을 경우에는 경고 메시지를 숨기고 싶을 수 있습니다. 이 경우 아래와 같이 **`-no-warn`** 옵션을 추가할 수 있습니다. (하지만, 이는 모든 경고 메시지를 숨기기 때문에 문제가 발생할 여지가 있습니다.)

    ```css
    cssCopy code
    git add --no-warn src/main/reactfront/

    ```
2. **원인 파악 및 해결**: 경고 메시지를 직접 읽고 원인을 파악한 뒤에 그 원인을 해결하는 것이 가장 좋은 방법입니다. 예를 들어, 대용량 파일에 대한 경고가 나오면 해당 파일을 \*\*`.gitignore`\*\*에 추가하거나, Git LFS를 사용하여 처리할 수 있습니다.
3.  **개행 문자 처리**: 개행 문자 관련 경고의 경우, Git 설정을 통해 자동으로 변환되게 설정할 수 있습니다:

    ```arduino
    arduinoCopy code
    git config --global core.autocrlf true

    ```

## 3. 깃허브에 프로젝트를 올렸을 때 특정 파일이나 폴더만 제외 원인

### **`.gitignore` 파일**

* **`.gitignore`** 파일은 Git에서 추적하지 않아야 할 파일 및 디렉토리를 지정
* 리액트 프로젝트의 경우, 기본적으로 생성되는 **`.gitignore`** 파일에는 **`node_modules/`** 등의 디렉토리가 포함되어 있어 이 디렉토리의 파일들은 Git에 포함되지 않습니다. 프로젝트 루트 또는 리액트 폴더 내에 **`.gitignore`** 파일이 있는지 확인하고 해당 파일 내용을 점검할 것

### **Git 초기화 위치**

* Git 저장소는 `git init`을 실행한 디렉토리를 기준으로 하위 디렉토리 및 파일들을 추적한다.
* 리액트 프로젝트 내에서 별도로 `git init`을 실행했을 경우, 리액트 프로젝트만 별도의 Git 저장소로 인식되어 상위 폴더의 Git에서는 무시될 수 있다.
* 이 경우, 리액트 프로젝트 디렉토리 내에 **`.git`** 디렉토리가 있는지 확인하고, 필요에 따라 삭제한 후 상위 디렉토리에서 다시 Git 명령을 실행할 것

### **커밋 누락**

* 리액트 파일을 변경한 후 `git add`와 **`git commit`** 명령을 실행하지 않았다면, 해당 변경사항은 커밋되지 않음
* 변경사항이 제대로 커밋되었는지 확인하려면 **`git status`** 명령을 사용하여 변경된 파일 목록을 확인할 수 있다.

### 💫 해결 방법

1. 프로젝트 루트 디렉토리에서 **`git status`** 명령을 실행하여 현재 Git의 상태를 확인
2. 리액트 프로젝트 폴더 내에 **`.git`** 디렉토리가 있는지 확인하고, 있다면 삭제
3. 프로젝트 루트에서 **`git add .`** 명령을 실행하여 모든 변경사항을 스테이징 영역에 추가
4. **`git commit -m "Add React files"`** 명령으로 변경사항을 커밋
5. **`git push`** 명령으로 변경사항을 깃허브에 푸시

## 4. 잔디(commit) 내용을 지키면서 두 개의 repository 합치기

### 로컬에 없는 경우.. 로컬에 clone 해줘야 함

1. 일단 내가 완성본을 넣을 레포지토리(레포1) 정하기
2. 나는 `teamproject_read_blog` 레포지토리를 완성본으로 하려고 한다.
3. git remote로 `teamproject_read_blog` 을 불러오기

* 나는 이름 지정을 잊어서 그냥 레포지토리 이름 대로 저장이 됨

```java
git remote add repo1 https://github.com/YourUsername/Repo1.git
```

![](https://velog.velcdn.com/images/prettylee620/post/5a14f0fb-61d2-45c9-828a-f68ce2c96a7e/image.png)

4. 그 폴더로 이동 한다

```java
cd teamproject_read_blog
```

5. `git fetch`를 해준다.

```java
git fetch teamproject_read_blog
```

![](https://velog.velcdn.com/images/prettylee620/post/224abfd0-e3f0-42ee-a6f4-ff7c214e25bc/image.png)

6. 로컬에 없기 때문에 위와 같은 것이 발생

* clone을 해줘야 함

![](https://velog.velcdn.com/images/prettylee620/post/41141087-9ac3-4153-b0ef-58c920fca130/image.png)

7. 그 후 폴더로 이동하고 `remote`해주기

```java
cd teamproject_read_blog
git remote add react_springboot_template https://github.com/GoldenPearls/react_springboot_template.git
```

![](https://velog.velcdn.com/images/prettylee620/post/604c5985-4521-4489-a608-2c6fc02e44ad/image.png)

8. `merge` 해주기

* 첫 번 째 시도 그냥 merge 해주기

```java
git merge react_springboot_template/master
```

![](https://velog.velcdn.com/images/prettylee620/post/94378bad-9aab-48a7-8561-13d185dbc44e/image.png)

* 두 번째 시도 : --allow-unrelated-histories
  * "refusing to merge unrelated histories"는 두 Git 저장소의 기록이 완전히 다르기 때문에 Git이 기본적으로 병합을 거부

```java
git merge react_springboot_template/master --allow-unrelated-histories
```

![](https://velog.velcdn.com/images/prettylee620/post/56856f86-0e5c-4869-951e-61cc71632a8e/image.png)

9. `push` 해주기

```java
git push origin main
```

![](https://velog.velcdn.com/images/prettylee620/post/5c7bbd03-1989-4e81-bdac-cfbae95a5289/image.png)

10. 결과 : 커밋도 잘 지키면서 합쳐짐

![](https://velog.velcdn.com/images/prettylee620/post/891c917c-35fb-43fa-b78d-3eb890e9f508/image.png)

## 5. 단어 알기

### 💫 remote란?

* **`remote`**는 Git에서 원격 저장소를 참조하는 단축 이름
* 원격 저장소는 인터넷이나 네트워크 어딘가에 존재하는 저장소를 의미하며, 여러 사람이 협업할 때 동일한 프로젝트에 대한 변경 사항을 교환하기 위해 사용
*   예를 들어, GitHub에서 프로젝트를 클론(clone)하면, 기본적으로 zip 파일로 받으면 문제가 생긴거 clone하면 고쳐지는 경우가 많다(실제 경험담...)



    > 프로젝트를 하면서 팀 공통 템플릿을 만들면서 알게 된 github 사용법 등을 정리해봤다 그 이후 계속 추가예정이고 팀 프로젝트 끝나면 1부터 10까지 정리해야겠다
