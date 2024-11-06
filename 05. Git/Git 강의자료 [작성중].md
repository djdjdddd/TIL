# Git 강의자료
1. Git이란 무엇이고, 워크플로우 및 리포지토리 구조에 대해 간단히 알아보겠습니다.
2. 자주 사용하는 Git 명령어를 알아보고, 실습해보면서 사용법을 익히겠습니다.

## 목차
- About Git
  - What is Git
  - Git Workflow & Structure
- 자주 사용하는 Git 명령어 (feat. 예시)
  - 저장하기
  - 가져오기
  - 병합하기
  - 임시저장
  - 되돌리기
  - 브랜칭
  - 로그 보기
- Reference

## 1. About Git
### (1) What is Git
지금까지 오늘날 환경에서 가장 널리 사용되는 최신 버전 제어 시스템은 Git입니다. Git은 원래 Linux 운영 체제 커널을 만든 사람으로 유명한 Linus Torvalds가 2005년에 개발한 활발하게 유지 관리되는 성숙한 오픈 소스 프로젝트입니다. 상용 프로젝트와 오픈 소스를 포함한 수많은 소프트웨어 프로젝트가 버전 제어에 Git을 사용하고 있습니다. Git을 사용해본 적이 있는 개발자 중에는 유능한 소프트웨어 개발 인재가 많으며 Git은 다양한 운영 체제와 IDE(통합 개발 환경)에서 잘 작동합니다.

분산 아키텍처를 사용하는 Git은 DVCS(분산된 버전 제어 시스템)의 한 예입니다. 한때 CVS나 Subversion(SVN이라고도 함)과 같이 널리 사용되었던 버전 제어 시스템에서 흔히 볼 수 있듯이 소프트웨어의 전체 버전 기록을 한 곳에 보관하는 것이 아니라 Git에서는 모든 개발자의 코드 작업 복사본 역시 모든 변경 사항의 전체 기록을 담을 수 있는 리포지토리입니다.

Git은 분산 외에도 성능, 보안, 유연성을 염두에 두고 설계되었습니다.

![image](https://github.com/user-attachments/assets/8bc36839-6893-47fa-9a66-5ce70fc39792)

---

### (2) Git Workflow & Structure
워크플로우를 한눈에 파악하기 좋은 자료로 각각의 `깃 명령어`와 함께 파악한다면 깃을 이해하는데 훨씬 도움이 될 것입니다.

![image](https://github.com/user-attachments/assets/752796e5-2cb8-4166-8cbe-a5edcd8caa37)

- Working Directory : 작업 공간
- Staging Area : 저장소에 커밋하기 전에 커밋을 준비하는 위치
- Local Repo (로컬 저장소) : 개인 전용 저장소
- Remote Repo (원격 저장소) : 원격 저장소. 파일이 원격 저장소 전용 서버에 관리되며 여러 사람이 함께 공유하기 위한 저장소

---

## 2. 자주 사용하는 Git 명령어 (feat. 예시)
### (1) 저장하기
- 로컬 저장소에 저장 : `git commit`
- 원격 저장소에 저장 : `git push`

---

### (2) 가져오기
원격 저장소(Remote Repo)에 있는 커밋을 가져오는 방법에는 2가지 방법이 있습니다. 
`pull`은 `fetch(가져오기)`를 한 다음 `merge(병합하기)`까지 하는 것이므로 
원격 저장소에 있는 커밋을 안전하게 가져오고 싶다면 `pull`보다 `fetch`를 우선적으로 고려해야 합니다.
- `git fetch`
- `git pull` = `git fetch` + `git merge`

> 중앙 저장소에 있는 이력들을 가져온다는 점에서 `svn update`와 유사

#### 상황 예시
`git fetch`를 하면 원격 저장소에 커밋(=저장)된 변경 사항들을 제 로컬 저장소로 가져올 수 있습니다.
```sh
PS C:\Users\SSS\IdeaProjects\TIL> git fetch
remote: Enumerating objects: 7, done.
remote: Counting objects: 100% (7/7), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 4 (delta 3), reused 0 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (4/4), 1.29 KiB | 147.00 KiB/s, done.
From https://github.com/djdjdddd/TIL
   5472843..72ec524  main       -> origin/main
```
이를 통해 (■사진 필요 - 아래 케이스스)
- 다른 사람이 원격에 올린 브랜치 목록을 가져오거나
- 원격 커밋 로그를 확인할 수 있습니다.

---

### (3) 병합하기
> 병합은 설명할 내용이 너무 방대하므로 자주 겪는 케이스만 다루고 넘어가겠습니다.

병합하는 명령어에는 크게 3가지가 있습니다.
병합할 땐 웬만하면 `merge` 사용을 권장합니다.
`rebase`나 `cherry-pick`은 특수한 경우에.. 특수한 목적만을 위해 사용하는 걸 추천합니다.
- `git merge`
  - 두 브랜치를 병합하는 기본 명령어
  - 현재 체크아웃된 브랜치에 대상 브랜치의 내용을 병합
- `git rebase` (권장 X)
  - 한 브랜치의 커밋을 다른 브랜치 위에 재배치하여 병합
  - 직선형 이력을 만들기 위해 주로 사용
- `git cherry-pick` (권장 X)
  - 특정 커밋만 선택하여 현재 브랜치에 병합

#### 예시
`feat/login`이라는 브랜치에서 로그인 기능을 개발했다고 가정해보겠습니다.
`운영(main)`에 배포할 기능을 모아 놓는 `릴리즈(rls/yyyyMMdd)` 브랜치에 해당 로그인 기능을 올리기 전에 우리는 테스트란 걸 해야 합니다.
`개발(dev)`에서 테스트를 하기 위해선 `feat/login` 브랜치를 병합할 필요가 있습니다.
어떻게 해야 할까요??

현재 `HEAD`가 `feat/login` 브랜치에 있다고 가정해봅시다.
```sh
git switch dev          # dev 브랜치로 스위칭
git pull origin dev     # 원격 dev를 로컬 dev와 동기화
git merge feat/login    # dev에 feat/login를 병합
git push origin dev     # 병합된 내용을 원격에 저장
```

---

### (4) 임시저장
#### 설명
`git stash`는 작업 중이던 변경 사항을 임시로 저장하는 기능입니다. 
`stack` 자료구조로 이루어져 있어서 가장 최근에 저장한 이력이 0번(`stash@{0}`)에 해당합니다.

주로 다음과 같은 경우에 사용합니다.
- 1번. 소스를 임시저장 해놨다가 나중에 다시 꺼내서 작업하려고 할 때
- 2번. 급하게 브랜치를 바꿔야 하는데 충돌이 나서 못 바꾸고 있는 상황

```sh
git stash                       # 현재 Working Directory에 있는 변경 사항을 스택에 임시저장
git stash -m '메시지 입력'       # 메시지와 함께 저장
git stash list                  # 저장된 stash list 보기
git stash pop                   # 가장 최근에 저장된 stash 적용 (스택에서 제거 O)
git stash apply stash@{index}   # 특정 stash 적용 (스택에서 제거 X)
```

#### 예시
저장할 때마다 스택에 쌓이게 되고, 각각 인덱스를 갖고 있습니다.
```sh
PS C:\Users\SSS\IdeaProjects\TIL> git stash -m '1번째 임시저장'
Saved working directory and index state On main: 1번째 임시저장
PS C:\Users\SSS\IdeaProjects\TIL> git stash -m '2번째 임시저장'
Saved working directory and index state On main: 2번째 임시저장
PS C:\Users\SSS\IdeaProjects\TIL> git stash -m '3번째 임시저장'
Saved working directory and index state On main: 3번째 임시저장
PS C:\Users\SSS\IdeaProjects\TIL> git stash list
stash@{0}: On main: 3번째 임시저장
stash@{1}: On main: 2번째 임시저장
stash@{2}: On main: 1번째 임시저장
```

---

### (5) 되돌리기
#### reset vs revert
- `git reset --hard <commit-hash>`
  - 특정 커밋 시점으로 되돌리면서 그동안 커밋된 모든 것을 완전히 날립니다.
  - 로컬 저장소에서만 사용하는 것이 정신건강에 좋습니다... (원격 저장소에 `reset --hard`를 할 경우 다른 사람이 친 커밋도 사라질 수 있기 때문에 매우 조심해야 합니다)
- `git revert <commit-hash>`
  - 특정 커밋 시점으로 되돌리면서 새로운 커밋을 생성합니다.
  - 명령어 이름만 다를 뿐 `commit`과 똑같으므로 혼동해선 안됩니다.
    - 자주 하는 실수 예시
  - 이미 `push`한 경우, 즉 원격 저장소에 올라간 커밋을 되돌리고 싶은 경우엔 `revert`를 쓰는 것이 안전합니다.

#### 주된 사용 사례
`git reset --hard <commit-hash>`
- `commit`을 잘못해서 되돌리고 싶은데, 굳이 `revert`를 해서 커밋 이력을 더럽히고 싶지 않을 때
- 로컬에선 커밋을 했다가 되돌리는 경우가 은근 많기 때문에 꽤 많이 사용하는 것 같습니다.

`git revert <commit-hash>`
- 이미 `push`가 된 커밋들 중에서 특정 커밋을 되돌리고 싶을 때
- 특히 `merge`를 하면서 생긴 커밋 이력을 `revert` 치면 해당 `merge`로 인해 딸려 들어온 커밋이 전부 롤백되기 때문에 상당히 유용합니다.
  - 사실 이래서 병합할 땐 `merge`를 쓰는 것이 관리 차원에서 좋습니다. `rebase`나 `cherry-pick`은 `revert`를 이용해서 롤백칠 때 쉽지 않습니다...

#### ■사진 필요 -> 로그에서 커밋해시를 보고, 되돌리기까지 일련의 과정

---

### (7) 로그 보기
`git log --oneline`을 통해 그동안 커밋 친 이력을 한눈에 볼 수 있습니다. 
인텔리제이를 사용한다면 GUI로 훨씬 더 쉽게 볼 수 있습니다.

주로
- 1번. `커밋 해시`를 알아야 하는 경우
  - 되돌리기 (revert, reset)
  - cherry-pick
  - 기타
- 2번. 말 그대로 로그 보고 싶을 때

#### CLI
```sh
PS C:\Users\SSS\IdeaProjects\TIL> git log --oneline
5472843 (HEAD -> main) Merge branch 'main' of https://github.com/djdjdddd/TIL
095df15 Git 관련 자료 바로가기 추가
f703025 Create 10.28 - NAS 관련 레퍼런스.md
cd8ec24 Create 10.24 - Query Tuning.md
ec9a77e Update Git Commit Message Convention.md
fcb8410 Docs: 내용 보완
b6f45e3 Update Git 브랜치 전략.md
4caa6fb Create Git Commit Message Convention.md
8fd090a Update Git 강의자료 [작성중].md
1c8a471 Create 10.17 - 리눅스, NAS.md
a0bbc39 Create 10.16 - 오라클 페이징 쿼리 2.md
bacb738 Update and rename 10.15 - 페이징 쿼리.md to 10.15 - 오라클 페이징 쿼리.md
ff3283d Create 10.15 - 페이징 쿼리.md
3bc7ad2 Create 10.10 - ORA-01861, 포팅, 세마포어, 카프카, 토스 NEXT.md
2f9c93b Delete 02. Backend/Java/[유데미]멀티스레딩, 병행성 및 성능 최적화.md
8e0410c Delete 02. Backend/Java/Optional.md
6ad6adf Delete 02. Backend/Java/Stream.md
7b0dca6 Delete 02. Backend/Java/Stream 내부에서 log 남기는 법■.md
```

#### GUI
![image](https://github.com/user-attachments/assets/7b5b31a8-29be-48cb-b2f9-39d72c0067f7)

---
---

## ■TODO. 스터디 2편에서 진행행

### (6) 브랜칭
git branch
git switch

---

### (7) Merge에 관한 자세한 설명명

#### `fast-forward merge`
현재 브랜치 팁에서 대상 브랜치까지의 선형 경로가 있는 경우 빨리 감기 병합이 발생할 수 있습니다. 브랜치를 "실제로" 병합하는 대신, Git은 현재 브랜치 팁을 대상 브랜치 팁까지 이동시키는 작업만으로(즉, "빨리 감기") 기록을 통합할 수 있습니다. 대상 브랜치에서 연결할 수 있는 모든 커밋을 이제 현재 커밋을 통해 사용할 수 있으므로 기록이 효과적으로 결합됩니다. 예를 들어, 일부 기능을 main 브랜치로 빨리 감기 병합하면 다음과 같습니다.

![image](https://github.com/user-attachments/assets/48a16999-1970-4aff-aa78-113cff9e83fa)

그러나 브랜치가 분기한 경우에는 빨리 감기 병합이 불가능합니다. 대상 브랜치에 대한 선형 경로가 없는 경우 Git은 3방향 병합을 통해 결합할 수밖에 없습니다. 3방향 병합은 전용 커밋을 사용하여 두 기록을 연결합니다. 3방향 병합이라는 이름은 Git이 브랜치 팁 두 개와 공통 원형 등 세 개의 커밋을 사용하여 병합 커밋을 만든다는 점에서 유래합니다.

![image](https://github.com/user-attachments/assets/f318945b-fd76-484b-9332-9a79c42d8f78)

#### 충돌 해결
The great part of Git's merging process is that it uses the familiar edit/stage/commit workflow to resolve merge conflicts. When you encounter a merge conflict, running the git status command shows you which files need to be resolved. For example, if both branches modified the same section of hello.py, you would see something like the following:

```
On branch main
Unmerged paths:
(use "git add/rm ..." as appropriate to mark resolution)
both modified: hello.py
```

#### 충돌을 표시하는 방식
일반적으로 `=======` 마커 앞의 콘텐츠는 수신 브랜치고 마커 뒤의 부분은 병합 브랜치입니다.
병합 충돌은 3방향 병합인 경우에만 발생합니다. 빨리 감기 병합에서는 충돌하는 변경 사항이 있을 수 없습니다.
```
here is some content not affected by the conflict
<<<<<<< main
this is conflicted text from main
=======
this is conflicted text from feature branch
>>>>>>> feature branch;
```

#### 요약
1. Git 병합은 커밋 시퀀스를 하나의 통합된 커밋 기록으로 결합합니다.
2. Git을 병합하는 두 가지 주요 방법은 빨리 감기 병합과 3방향 병합입니다.
3. 두 커밋 시퀀스에서 충돌하는 변경 사항이 없으면 Git은 커밋을 자동으로 병합할 수 있습니다.


#### Merge conflicts
![image](https://github.com/user-attachments/assets/9e1e2786-f538-45a4-a21e-bfbdd179b803

---

## Reference
- [아틀라시안 공식자료](https://www.atlassian.com/git/tutorials/saving-changes/git-stash)
- [인파 : git 개념 & 원리 (그림으로 알기쉽게 비유 😃)](https://inpa.tistory.com/entry/GIT-%E2%9A%A1%EF%B8%8F-%EA%B0%9C%EB%85%90-%EC%9B%90%EB%A6%AC-%EC%89%BD%EA%B2%8C%EC%9D%B4%ED%95%B4#%EA%B7%B8%EB%A6%BC%EC%9C%BC%EB%A1%9C_%EC%9D%B4%ED%95%B4%ED%95%98%EB%8A%94_%EB%B2%84%EC%A0%84_%EA%B4%80%EB%A6%AC_%EC%9B%90%EB%A6%AC)
- [Git merge,rebase,cherry-pick 설명 + 그림 자료](https://hqjang.tistory.com/114)
- [6 Git Cherry-Pick Commands You Should Know](https://www.hatica.io/blog/git-cherry-pick-commands/)
