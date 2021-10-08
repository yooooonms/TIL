# Linux 기본 명령어  

#### echo  

텍스트를 화면에 출력한다.  

```shell
$ echo hello world
hello world
```

#### pwd (print working directory)  

현재 작업 중인 디렉토리 정보를 출력한다.  

```shell
$ pwd
/Users/hello
```

#### cd (chagne directory)  

경로 이동  

절대 경로와 상대 경로로 이동이 가능하다.  

- `.` (현재 디렉토리)
- `..` (부모 디렉토리)
- `~` (홈 디렉토리)
- `-` (이전 디렉토리)

```shell
$ cd desktop
$ pwd
/Users/hello/desktop

$ cd ..
$ pwd
/Users/hello
```

#### ls (list)  

디렉토리 목록 확인  

```shell
$ ls

# 어떤 옵션을 사용하는지에 따라 출력되는 정보가 달라진다.
-l : long format으로 자세한 파일 정보를 표시
-f : 정렬하지 않는다.
-a : 숨김파일 까지 모두 표시 ('.', '..' 디렉토리 포함)
-A : 숨김파일 까지 모두 표시 ('.', '..' 디렉토리 제외)
-r : 역순 정렬
-R : 모든 하위디렉토리에 대해 재귀적으로 출력한다.
-t : 파일 수정 시간별로 정렬
-h : Human readable format으로 파일 사이즈를 사람이 읽기 편한 형태로 출력한다.
-S : 파일 사이즈별로 정렬
-s : -l 옵션과 같이 사용한다. 파일 사이즈를 블록 단위로 표시한다.
```

#### touch  

빈 파일을 생성한다.  

```shell
$ touch hello.md
```

#### file  

해당 파일이 어떤 종류의 파일인지 확인한다.  

```shell
$ file hello.md
```

#### cat  

파일을 읽을 수 있다. 파일의 내용을 표시할 뿐만 아닌 여러 파일을 결합해 출력할 수 있다.  

```shell
$ cat hello.md
```

큰 파일을 보는 데는 좋지 않고 주로 짧고 간략한 파일을 확인하는데 적합하다.  

#### less  

짧은 텍스트 파일보다는 내용이 많은 텍스트 파일을 볼 경우에 사용된다.  

텍스트가 페이지 방식으로 표시되어 한 페이지씩 탐색 할 수 있다.  

```shell
$ less hello.md

# 사용할 수 있는 옵션
q: less를 나와 다시 Shell로 돌아간다.
g: 텍스트 파일의 시작으로 이동한다
G: 텍스트 파일의 끝으로 이동한다.
/: 텍스트 문서 내에서 특정 텍스트를 검색할 수 있다. 
ex) hello를 검색하고 싶다면 /hello를 입력하고 Enter를 누르면 된다.
h: 도움말
```

#### history  

이전에 입력한 명령어들을 확인할 수 있다.  

```shell
$ history
```

#### clear  

화면에 보이는 내용을 모두 지운다.  

```shell
$ clear
```

#### cp (copy)  

파일 혹은 디렉토리를 복사한다.  

```shell
# work.md는 복사할 파일이고, /Users/yoonminsoo/Desktop/Docs는 파일을 복사할 위치이다.
$ cp work.md /Users/yoonminsoo/Desktop/Docs
```

와일드카드를 사용해 여러 파일과 디렉토리를 복사할 수 있다.  

- `*` 길이가 0 이상인 임의의 문자열
- `?` 임의의 한 문자이다.

```shell
# 현재 디렉토리에서 확장자가 md인 모든 파일이 Docs에 복사된다.
$ cp *.md /Users/yoonminsoo/Desktop/Docs
```

`-r` 옵션을 사용해 디렉토리와 해당 디렉토리 내부에 있는 모든 파일을 복사할 수 있다.  

```shell
$ cp -r Docs /Users/yoominsoo/Develop
```

실수로 덮어쓰기를 방지하기 위해 `-i` 옵션을 사용해 파일을 덮어쓰기 전에 메시지를 표시할 수 있다.  

#### mv (move)  

파일을 이동하거나 이름을 바꾸는데 사용한다. `cp` 명령과 비슷하다.  

```shell
# 파일명을 hello.md에서 world.md로 바꾼다.
$ mv hello.md world.md

# hello.md 파일을 /Users/yoonminsoo/Docs로 이동한다.
# 복수의 파일도 가능하다.
$ mv hello.md /Users/yoonminsoo/Docs
$ mv hello.md world.md /Users/yoonminsoo/Docs

# 디렉토리의 이름을 바꿀 수 있다.
$ mv Docs Develop
```

실수로 덮어쓰기를 방지하기 위해 `-i` 옵션을 사용해 파일을 덮어쓰기 전에 메시지를 표시할 수 있다.  

#### mkdir (make directory)  

디렉토리를 생성한다. 여러 디렉토리를 동시에 생성하는 것도 가능하다.  

```shell
$ mkdir docs develop

# -p 하위 디렉토리를 함께 생성할 수 있다.
$ mkdir -p docs/tools/bootstrap
```

#### rm (remove)  

파일과 디렉토리를 제거하는데 사용된다.  

```shell
$ rm hello.md

# -f 강제 파일 제거
$ rm -f hello.md

# mv, cp와 마찬가지로 -i를 사용해 제거하는지 묻는 메시지 출력
$ rm -i hello.md

# -r 옵션을 추가해 디렉토리를 삭제 해당 디렉토리의 파일과 하위 디렉토리 모두 삭제
$ rm -r docs
```

#### find  

파일을 검색하는데 사용한다.  

검색을 위해서는 검색할 디렉토리와, 검색 대상을 지정해야한다.  

```shell
# /Users에서 hello.md라는 이름을 가진 파일을 검색한다.
$ find /Users -name hello.md
```

---

#### 📌 Reference  

- <https://gomguard.tistory.com/73>
- <https://itholic.github.io/linux-basic-command/>