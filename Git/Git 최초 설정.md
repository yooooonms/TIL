# 최초 설정

### 기본 설정

```git
# 사용자 이름 설정
$ git config --global user.name <username>

# 사용자 이메일 설정
$ git config --global user.email <useremail>
```

`--global`은 전역 설정으로 모든 저장소에 적용되는 설정으로 최초 한번만 설정해주면 된다. 저장소마다 다르게 설정하기 위해서는 `--global` 옵션을 빼주면 된다.

이 외에도 기본으로 사용할 편집기, 브랜치 이름 등을 지정할 수 있다.

```git
# 기본 브랜치 이름 설정
$ git config --global init.defaultBranch <branch>

# 기본 편집기 설정
$ git config --global core.editor <editor>
```

설정 정보를 제거하기 위해서는 `--unset` 옵션을 사용하면 된다.

```git
$ git config --global --unset user.name
$ git config --global --unset user.email
```

`--global` 옵션을 적용하면 전역으로 설정된 설정이 제거된다. 각 저장소마다 다르게 설정된 정보를 삭제하려면 `--global` 옵션을 제거하면 된다.

설정 목록을 확인하기 위해서는 `git config --list` 명령을 사용 하면 된다. 이는 `--global` 옵션이 적용된 설정을 보여준다.

특정 저장소에서 해당 명령을 실행하면 해당 저장소에 적용된 모든 설정을 보여준다. 옵션에 지정된 값을 보려면 `git config <key>` 명령을 사용하면 된다.

### CSRF 개행 문자 차이 해결하기

Window에서는 개행 문자로 CR(Carrige-Refurn, \r)과 LF(Line Feed, \n)를 사용하지만  Linux 또는 Mac OS는 LF(Line Feed, \n)만 사용한다.

코드의 변경점은 없는데 운영체제간 개행 문자의 차이로 인해 변경으로 착각해 파일이 변하는 문제가 생길 수 있다. 이런 문제를 해결하기 위해서는 운영체제가 달라도 문제가 발생하지 않도록 CRLF 설정을 해주는 것이 좋다.

#### 해결 방법

- **core.autocrlf = false:** 기본 설정으로 CRLF를 사용하든 LF를 사용하든 git은 신경쓰지 않는다.
- **core.autocrlf = true:** CRLF를 LF로 변경한다.
- **core.autocrlf = input:** LF를 사용한다.

```git
# Windows
$ git config --global core.autocrlf = true
```

Windows에서는 CRLF를 사용하기 때문에 저장소에서 가져올 때 LF를 CRLF로 변경하고, CRLF를 LF로 변경할 수 있도록 true로 설정한다.

```git
# Linux or Mac OS
$ git config --global core.autocrlf = input
```

Linux 또는 Mac OS는 LF(Line Feed, \n)만 사용하기 때문에 input으로 설정해 준다.

---

#### Reference

- <https://git-scm.com/book/ko/v2/시작하기-Git-최초-설정>
- <https://www.lesstif.com/gitbook/git-crlf-20776404.html>