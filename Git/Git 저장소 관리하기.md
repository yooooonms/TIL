# 저장소 관리하기

저장소는 자신의 컴퓨터인 로컬 저장소와 어떤 서버에 있는 원격 저장소로 나눌 수 있다.

본인 컴퓨터의 작업 폴더에 `git init` 명령으로 로컬 저장소를 생성할 수 있다. 이제부터 해당 폴더의 파일들은 git이 관리하게 된다.

### Git의 작업 공간

<img src="/Git/image/git-worktree.png" width="400" height="250">

git은 Working Directory, Staging Area, .git(Repository) 세가지의 작업 공간을 가지고 있다.

#### - Working Directory

Working Directory는 작업 폴더를 말하며 본인의 프로젝트를 진행하는 폴더를 말한다. 작업 폴더 내부에는 `.git` 이라는 폴더가 존재한다.

#### - Staging Area

`.git` 디렉토리안에 단순한 파일로 존재하며 곧 커밋할 파일에 대한 정보를 저장한다. 사용자가 새로운 파일을 생성 또는 기존 파일을 수정해 `git add`한 파일들이 저장된다.

#### - .git(Repository)

Git의 핵심이며 워킹 디렉토리의 변경 이력들이 저장되어 있다. `git commit` 명령을 통해 Staging Area에 존재하는 스냅샷을 하나의 버전으로 만들어 저장한다.

`git init` 명령을 통해 생성하거나 다른 서버에 있는 원격 저장소를 `clone` 할 때 만들어진다.

### Git에서 관리하는 여러가지 파일의 상태

<img src="/Git/image/git-lifecycle.png" width="500" height="250">

작업 폴더의 모든 파일은 크게 `Tracked`와 `Untracked`로 나눌 수 있다.

`Untracked` 파일은 git의 관리 대상이 아닌 파일로 어떠한 변경사항도 추적하지 않는다. 보통 새로 생성한 파일들이 `Untracked` 상태이다.

여기서 `Tracked` 상태는 다시 3가지로 나눌 수 있다.

- Staged
- Modified
- Unmodified

워킹 디렉토리의 파일은 매순간 위 **Untracked, Staged, Modified, Unmodified** 중 하나의 상태에 있다.

파일의 상태를 확인하려면 `git status` 명령을 사용하면 된다.

#### 1. Staged

Staging Area에 올라간 상태의 파일을 말한다. 새로 파일을 생성해 git add를 해주거나 Modified 상태의 파일을 git add 해주면 Staged 상태의 파일이 된다.

#### 2. Modified

파일의 최신 커밋과 비교했을 때 바뀐게 조금이라도 있으면 Modified 상태이다. Tracked 상태이지만 아직 Staged 상태는 아니기 때문에 git add 명령을 통해 Staged 상태로 만들 수 있다.

#### 3. Unmodified

파일의 최신 커밋과 비교했을 때 바뀐게 없으면 UnModified 상태이다. 처음 저장소를 Clone 하면 모든 파일은 Tracked 이면서 Unmodified 상태이다.

## 로컬 저장소와 원격 저장소 연결하기

로컬에서 프로젝트를 진행할 폴더에 `git init` 명령을 실행한 후 로컬 저장소로 만든다.

```text
# git remote add <원격 저장소 이름> <원격 저장소 주소>
$ git remote add origin https://github.com/yooooonms/test.git
```

위 명령으로 원격 저장소 이름과 원격 저장소 주소를 지정하면 된다. 원격 저장소 이름은 보통 origin을 사용하며 다른 걸로 지정할 수 있다.

이후 정상적으로 연결됬는지 확인하기 위해 `git remote -v` 명령을 사용하면 설정해둔 원격 저장소 이름과 원격 저장소 주소를 확인할 수 있다.

원격 저장소와 연결을 해제하려면 `git remote remobe <원격 저장소 이름>` 명령을 실행해 주면 된다.

## 로컬 저장소에서 원격 저장소로 파일 보내기

파일을 원격 저장소로 보내기 위해서는 몇가지 과정을 진행해야 한다.

새롭게 생성한 파일 또는 기존 파일을 수정한 경우 `git add <디렉토리경로/파일명>` 명령을 통해 Staging Area에 파일을 올려야 한다.

git add 하기 전에 git status 명령으로 파일의 상태를 확인할 수 있다. 현재 디렉토리의 파일 전체를 스테이징 영역에 추가하려면 `git add .` 명령을 사용하면 된다.

추가로 작업 디렉토리의 추가되거나 변경된 파일을 전체를 스테이징 영역에 추가하려면 `git add -A` 명령을 사용하면 된다.

```text
git add . 과 git add -A의 차이점은 git add -A는 작업 디렉토리 상에 어디에 위치하든 항상 동일하게 모든 변경 내용을 스테이징으로 넘긴다.
반면 git add .는 명령어를 실행한 디렉토리 이하에서 발생한 변경 내용만 포함하며, 해당 디렉토리 기준으로 상위 디렉토리의 변경 내용을 포함하지 않는다.
디렉토리의 루트에서 git add . 명령을 실행하면 git add -A와 동일한 효과를 가진다.
```

`git add -p` 명령을 사용하면 각 변경 사항을 하나씩 확인하며 스테이징 영역에 추가하거나 제외할 수 있다.

파일을 스테이징 영역에 추가했다면 파일의 변경 이력을 관리하기 위해 `commit` 이라는 작업을 진행해야 한다.

`git commit -m <메시지>` 명령을 사용해 해당 파일에 대한 내용을 간단하게 한 줄로 표시할 수 있다. 한 줄이 아닌 해당 파일에 대한 상세한 내용을 기록하려면 `git commit` 명령을 실행하면 된다. 해당 명령은 편집기로 커밋 메시지를 작성할 수 있는데 시스템의 기본 편집기가 실행된다.

메시지를 수정하려면 `git commit -ammend -m <수정할 메시지>` 명령을 사용하면 된다. 가장 마지막으로 commit한 내용의 메시지가 수정된다.

```text
# 기본 편집기를 변경하는 명령
git config —global core.editor <editor>
```

마지막으로 `git push <원격 저장소 이름> <브랜치 이름>` 명령으로 원격 저장소에 변경 내용을 반영하면 된다.

매번 변경 내용을 반영할 때마다 원격 저장소 이름과 브랜치 이름을 입력하는 것이 귀찮아 생략하고 싶다면 `git push -u <원격 저장소 이름> <브랜치 이름>` 명령을 사용하면 된다.

해당 명령을 사용하고 나서는 원격 저장소 이름과 브랜치 이름을 생략해도 된다.

### 원격 저장소에 변경된 내용을 반영하는 과정 정리

- 새롭게 추가되거나 변경된 내용의 파일을 `git add .` 스테이징 영역에 추가한다.
- `git commit -m <메시지>` 명령으로 변경 사항에 대한 메시지를 추가한다.
- `git push <원격 저장소 이름> <브랜치 이름>` 명령으로 원격 저장소에 변경 내용을 반영한다.

적절하게 옵션을 사용하면 더욱 효율적으로 진행할 수 있다.

----

#### Reference

- <https://git-scm.com/book/ko/v2/%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-Git-%EA%B8%B0%EC%B4%88>
- <https://git-scm.com/book/ko/v2/Git%EC%9D%98-%EA%B8%B0%EC%B4%88-%EC%88%98%EC%A0%95%ED%95%98%EA%B3%A0-%EC%A0%80%EC%9E%A5%EC%86%8C%EC%97%90-%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0>