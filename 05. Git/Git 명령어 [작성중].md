# Git 명령어 [작성중]

## Reference
- https://urbanbase.github.io/dev/2021/01/15/GitCommand.html
- https://dololak.tistory.com/303
- https://dololak.tistory.com/304
- https://git-scm.com/book/ko/v2
- https://medium.com/@joongwon/git-git-%EB%AA%85%EB%A0%B9%EC%96%B4-%EC%A0%95%EB%A6%AC-c25b421ecdbd

## git init
git 초기화. 
git으로 버전관리를 할 경로에서 사용합니다.
`.git` 폴더가 생성되며 아래와 같은 구조입니다.

---

## git status 
현재 git 프로젝트에서 파일들의 상태를 보여줍니다. 
git의 영역과 상태에 대한 자세한 설명은 아래 두 문서를 참고해주세요.

---

## git add
파일의 변경 내용을 스테이징 영역(staging area)에 추가하기 위해 사용하는 명령어입니다.
스테이징 영역으로 추가된 변경 이력만 `commit`할 수 있습니다.
```shell
# 모든 변경점 추가
git add -A

# 현재 경로의 모든 변경점 추가
git add .

# README.md 파일만 추가
git add README.md
```

---

## git rm
파일을 지우거나 스테이지에서 해제할 때 사용합니다. 
```shell
# 파일 삭제
git rm README.md

# README.md 파일을 추적되지 않은 상태로 만듦
git rm --cached README.md
```

---

## git restore (2.23)
워킹 트리(Wroking tree)의 변경된 파일을 복원해주는 역할을 합니다.
```shell
# Unstaged 상태의 변경 파일을 원상복구
git restore [파일명]

# git add로 Staging 된 파일을 Unstaged 상태로 되돌림
git restore --staged [파일명]
```

---

## git clean
추적되지 않은 상태(`untracked`)의 파일을 삭제합니다. 
삭제가 되면 복구할 수 없으니 `stash`를 고려해보는 것도 좋습니다.
```shell
# 디렉토리를 제외한 파일만 삭제
git clean -f

# 디렉토리 포함 삭제
git clean -f -d

# .gitignore 에 설정된 파일도 삭제
git clean -f -d -x

# 가상 실행
git clean -n  # 이게 뭐지??
```

---

## git commit
변경된 내용을 저장합니다.
```shell
# 메시지와 함께 커밋
git commit -m 'First commit'

# 신규 파일을 제외한 변경사항을 Staging 후 커밋
git commit -a

# 이전 커밋 변경
git commit --amend
```

### git commit --amend


---

## git log
`commit` 목록을 볼 수 있습니다. 
굉장히 많은 옵션이 있으니 `git log --help` 명령어로 자신에게 맞는 조합을 활용하시기 바랍니다.

```shell
# branch 그래프를 추가하여 보기
git log --graph

# 모든 branch 보기
git log --all

# commit 메시지 제목만 한줄로 보기
git log --oneline
```

### git log --oneline


---

## git show
`commit`의 상세 정보를 확인합니다.

```shell
# 현재 branch의 가장 최근 commit 정보를 확인
git show

# 특정 commit 정보를 확인
git show [commit 해시값]

# 특정 branch의 가장 최근 commit 정보를 확인
git show [branch 명]
```

---

## git reset HEAD [file]
- `commit`을 취소할 수 있습니다.

```shell
# commit을 취소하고 해당 파일들은 스테이징 영역에 보존
git reset --soft HEAD^

# commit을 취소하고 해당 파일들은 Unstaging
git reset --mixed HEAD^
git reset HEAD^

# commit을 취소하고 해당 파일들의 변경점 삭제
git reset --hard HEAD^
```

- `push`를 취소할 수 있습니다.

```shell
git reset HEAD^
git push -f origin 브랜치명
git pull
```

### git reset - 테스트 필요


---

## git remote
원격 저장소(remote repository)를 관리하는 명령어입니다.
```shell
# 설정된 원격 저장소 보기
git remote -v

# xxx 라는 이름으로 원격 저장소 추가하기 (정확히는 로컬(local)에서 원격(remote) 저장소를 연결하는 것이고, 일종의 별명/별칭을 지정하는 것)
git remote add xxx <원격 저장소 URL>

# 삭제
git remote remove xxx   # xxx 는 원격 저장소 이름
git remote rm xxx
```

### 실제로 추가/삭제 하는 예시 보여줄 필요성 있음
- 디폴트로 origin 이라는 이름으로 생성되는 것 외에 내가 다른 이름으로 추가할 수 있다는 점
- 삭제하는 방법

---

## git push
원격 저장소(remote repository)에 코드 변경분을 업로드 합니다.
```shell
# 기본 사용법
git push [저장소명] [branch]

# 최초 1회 저장소, branch 지정, 이후 생략 가능
git push -u [저장소명] [branch]

# 로컬에서 생성한 branch를 push
git push --set-upstream [저장소명] [branch]
```

### git push --set-upstream [저장소명] [branch]
예를 들어, 로컬(local)에서 `feature-login`이라는 브랜치를 새로 생성한 후 작업을 했다고 가정해보겠습니다.
작업물을 `push`하려고 할 때 원격(remote) 저장소에 `feature-login` 브랜치가 없다면 오류가 발생하며 `push`가 되지 않습니다. 
이 경우 `git push --set-upstream [저장소명] feature-login` 한다면 branch 생성이 되면서 `push`가 이뤄지게 됩니다.

---

## git branch
branch에 관련한 명령어입니다.
```shell
# 로컬 branch 목록 확인
git branch

# 원격 저장소를 포함한 모든 branch 목록 확인
git branch -a

# 원격 저장소의 branch 목록 확인
git branch -r

# xxx 라는 이름의 branch 생성하기
git branch xxx

# xxx 라는 이름의 로컬 branch를 origin 이라는 원격 저장소의 yyy branch에 연결
git branch --set-upstream-to=origin/yyy xxx   (브랜치명은 서로 같은 걸 쓰는게 좋지 않을까??)

# xxx 라는 이름의 branch 삭제
git branch -d xxx

# xxx 라는 이름의 branch 강제 삭제
git branch -D xxx
```

### `-d` vs `-D`
대문자(`-D`)를 사용할 경우 강제 삭제가 되기 때문에 이 점을 유의해야 합니다.

---

## git switch (2.23)
branch를 변경하는 명령어입니다. `checkout` 명령어에서 복원하는 기능을 제거한 것입니다.
```shell
# xxx 라는 이름의 branch로 변경하기
git switch xxx

# xxx 라는 branch를 새로 생성 및 변경하기
git switch -c xxx
```

---

## git checkout 
branch를 변경하고 워킹 트리(Working tree)에서 변경점을 복원하는 명령어입니다. 
`checkout`보단 `switch`나 `restore` 명령어를 사용하는 걸 추천합니다.
```shell
# xxx 라는 이름의 branch로 변경하기
git checkout xxx

# xxx 라는 이름의 branch를 새로 생성 및 변경하기
git checkout -b xxx

# Unstaged 상태의 파일을 원래대로 되돌림
```

---

## git fetch
원격 저장소(remote repository)의 데이터를 가져옵니다. 
`pull`과 다른 점은 `fetch`는 데이터를 가져오기만 할 뿐 병합(`merge`)하지 않는다는 점에서 차이가 있습니다.
```shell
# origin 이라는 원격 저장소의 데이터를 가져옴
git fetch origin

# 모든 원격 저장소의 데이터를 가져옴
git fetch --all

# 원격 저장소에서 삭제된 branch를 로컬에서도 삭제
git fetch --prune
```

---

## git pull
원격 저장소(remote repository)의 데이터를 가져온 후 로컬 branch에 병합(`merge`)합니다.
```shell
# git fetch --all && git pull 과 같습니다 (????????????)
git pull --all
```

---

## git stash
현재 작업중인 변경점을 임시저장 하거나 불러올 수 있습니다. 
현재와 다른 branch로 가서 작업을 하기 전에 사용하면 유용합니다.
```shell
# 현재 변경점 test 라는 이름으로 저장하기
git stash save test

# stash 목록(stack) 확인하기
git stash list

# test 라는 stash를 불러와 적용하기
git stash apply test

# test 라는 stash를 불러와 적용하는데, Staged 상태까지 적용하기
git stash apply test --index

# 가장 최근의 stash를 가져와 적용하고 스택(목록)에서 삭제하기
git stash pop

# 가장 최근의 stash 제거하기
git stash drop

# test 라는 stash를 제거하기
git stash drop test
```

### 좀 더 확인해봐야 할 점
- `git stash save yong` 명령어를 통해 yong 이라는 이름으로 임시저장 하였음.
- `git stash apply yong` 명령어를 통해 yong 이라는 이름의 stash를 불러와 적용하려고 했더니 `error: yong is not a valid reference`가 발생.
- `git stash pop` 명령어로 가장 최근의 stash를 가져와 적용하고 스택에서 삭제하였더니 제대로 작동하였음.
- 어째서...?

---

## git blame
특정 파일의 수정 이력을 확인할 수 있습니다.
각 라인별로 누가, 언제 마지막으로 수정했는지 알 수 있습니다.
```shell
# test.txt 파일의 수정 이력을 확인
git blame test.txt

# test.txt 파일의 5붜 10번 라인까지만 확인
git blame -L 5,10 test.txt

# 파일명이 변경됐다면 변경 전의 파일명과 함께 확인
git blame -C newTest.txt

# 공백 변경을 무시하고 확인
git blame -w test.txt
```

---

## git diff
소스를 비교하여 볼 수 있습니다.
```shell
# 마지막으로 커밋된 소스와 현재 Unstaged 상태의 변경점과 비교
git diff

# 마지막으로 커밋된 소스와 현재 Staging 된 변경점과 비교
git diff --staged

# 커밋간 비교
git diff [커밋해시1]..[커밋해시2]

# branch간 비교
git diff [branch1] [branch2]
```

---

## git revert
지정한 커밋으로 되돌려 커밋합니다. 
되돌린 이력이 남기 때문에 충돌이 발생할 위험이 적습니다.
협업 상황에서는 `reset`을 사용하지 말고, `revert`를 사용하는 걸 권장합니다.
```shell
# 특정 커밋으로 되돌리고 커밋
git revert [커밋해시]

# 특정 태그로 되돌리고 커밋
git revert [태그명]

# 특정 커밋으로 되돌리지만 커밋은 하지 않은 채로 Staging 상태
git revert [커밋해시] -n
```

### 테스트 필요

---

## git tag
특정 커밋에 표기하는 기능입니다.
주로 릴리즈 시 이용합니다.
2가지 종류가 있는데 Lightweight 태그와 Annotated 태그입니다.
Lightweight 태그는 단순히 버전 등의 이름을 남길 때 사용합니다.
Annotated 태그는 만든 사람의 이름, 이메일, 날짜, 메시지까지 저장하며 GPG(GNU Privacy Guard)로 서명까지 가능합니다.
```shell
# 현재 HEAD에 v1.0.0이라는 Lightweight 태그 생성
git tag v1.0.0

# 현재 HEAD에 v1.0.0이라는 Annotated 태그 생성
git tag -a v1.0.0 -m 'message'

# 특정 커밋에 v1.0.0이라는 Lightweight 태그 생성
git tag v1.0.0 [커밋해시]

# v1.0.0 태그를 원격 저장소에 푸시하기
git push origin v1.0.0

# 모든 로컬 태그를 원격 저장소에 푸시하기
git push origin --tags

# 로컬의 v1.0.0 삭제
git tag -d v1.0.0

# 원격 저장소의 v1.0.0 태그 삭제
git push -d origin v1.0.0
```

---

## git merge
현재 브랜치를 특정 브랜치의 소스와 병합합니다. 
`merge`하기 전엔 워킹 디렉토리를 `stash` 등으로 깔끔하게 정리하고 진행하는 것을 추천합니다.
```shell
# main branch를 병합
git merge main

# 병합 충돌(Conflict) 발생시 취소
git merge --abort

# 공백으로 인한 병합 충돌을 무시하고 병합
git merge -Xignore-all-space
```

---

## git rebase
`rebase`는 알아두면 좋으니 간단하게 정리하겠습니다.
`merge`처럼 branch를 합치는 기능을 하지만, 작동 방식이 조금 다릅니다.
- 자세한 설명과 개념 : https://git-scm.com/book/ko/v2/Git-브랜치-Rebase-하기

이런 경우에 사용하면 좋을 것 같습니다.
- 예) master 브랜치에서 feature 브랜치로 분기하여 커밋 후, master 브랜치의 새로운 커밋이 있을 때 feature 브랜치가 master 브랜치의 새로운 커밋을 병합해야 할 때, merge 커밋을 남기기 싫을 경우.
- 쉽게 말해서, 작업하는 branch를 병합할 branch에 병합 이력없이 변경 커밋만 남기고 싶을 때 사용하면 좋습니다.

```shell
# 모든 원격 저장소의 데이터를 가져옴 (최신화)
git fetch --all

# 원격 저장소(origin)의 main 브랜치로 rebase
git rebase origin/main # git merge origin/main 과 결과는 같으나, 병합 이력 커밋이 없다
```
중요한 점은 원격 저장소에 올라가 사용 중인 커밋을 rebase 하는 것은 최대한 지양해야 한다는 것입니다.
