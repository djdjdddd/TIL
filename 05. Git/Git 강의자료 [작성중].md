# Git 강의자료
1. Git이란 무엇이고, 워크플로우 및 리포지토리 구조에 대해 간단히 알아보겠습니다.
2. 자주 사용하는 Git 명령어를 알아보고, 실습해보면서 사용법을 익히겠습니다.

## 목차
- About Git
  - What is Git
  - Git Workflow & Structure
- 자주 사용하는 Git 명령어 (feat. 상황 예시)
  - 저장하기
  - 가져오기
  - 병합하기
  - 임시저장
  - 되돌리기
  - 브랜칭
  - 로그 보기

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

## 2. 자주 사용하는 Git 명령어 (feat. 상황 예시)
### (1) 저장하기
- 로컬 저장소에 저장 : `git commit`
- 원격 저장소에 저장 : `git push`

### (2) 가져오기
원격 저장소(Remote Repo)에 있는 커밋을 가져오는 방법에는 2가지 방법이 있습니다. 
`pull`은 `fetch(가져오기)`를 한 다음 `merge(병합하기)`까지 하는 것이므로 
원격 저장소에 있는 커밋을 안전하게 가져오고 싶다면 `pull`보다 `fetch`를 우선적으로 고려해야 합니다.
- `git fetch`
- `git pull` = `git fetch` + `git merge`

> 중앙 저장소에 있는 이력들을 가져온다는 점에서 `svn update`와 유사

#### 예시

git fetch -a 

### (3) 병합하기

### (4) 임시저장
git stash

### (5) 되돌리기
- git reset --hard 
- git revert 

### (6) 브랜칭
git branch
git switch

### (7) 로그 보기
git log --oneline


---

## Reference
- [아틀라시안 공식자료](https://www.atlassian.com/git/tutorials/saving-changes/git-stash)
- [인파 : git 개념 & 원리 (그림으로 알기쉽게 비유 😃)](https://inpa.tistory.com/entry/GIT-%E2%9A%A1%EF%B8%8F-%EA%B0%9C%EB%85%90-%EC%9B%90%EB%A6%AC-%EC%89%BD%EA%B2%8C%EC%9D%B4%ED%95%B4#%EA%B7%B8%EB%A6%BC%EC%9C%BC%EB%A1%9C_%EC%9D%B4%ED%95%B4%ED%95%98%EB%8A%94_%EB%B2%84%EC%A0%84_%EA%B4%80%EB%A6%AC_%EC%9B%90%EB%A6%AC)
- [Git merge,rebase,cherry-pick 설명 + 그림 자료](https://hqjang.tistory.com/114)
- [6 Git Cherry-Pick Commands You Should Know](https://www.hatica.io/blog/git-cherry-pick-commands/)

---

## Merge

##### `fast-forward merge`
현재 브랜치 팁에서 대상 브랜치까지의 선형 경로가 있는 경우 빨리 감기 병합이 발생할 수 있습니다. 브랜치를 "실제로" 병합하는 대신, Git은 현재 브랜치 팁을 대상 브랜치 팁까지 이동시키는 작업만으로(즉, "빨리 감기") 기록을 통합할 수 있습니다. 대상 브랜치에서 연결할 수 있는 모든 커밋을 이제 현재 커밋을 통해 사용할 수 있으므로 기록이 효과적으로 결합됩니다. 예를 들어, 일부 기능을 main 브랜치로 빨리 감기 병합하면 다음과 같습니다.

![image](https://github.com/user-attachments/assets/48a16999-1970-4aff-aa78-113cff9e83fa)

그러나 브랜치가 분기한 경우에는 빨리 감기 병합이 불가능합니다. 대상 브랜치에 대한 선형 경로가 없는 경우 Git은 3방향 병합을 통해 결합할 수밖에 없습니다. 3방향 병합은 전용 커밋을 사용하여 두 기록을 연결합니다. 3방향 병합이라는 이름은 Git이 브랜치 팁 두 개와 공통 원형 등 세 개의 커밋을 사용하여 병합 커밋을 만든다는 점에서 유래합니다.

![image](https://github.com/user-attachments/assets/f318945b-fd76-484b-9332-9a79c42d8f78)

##### 충돌 해결
The great part of Git's merging process is that it uses the familiar edit/stage/commit workflow to resolve merge conflicts. When you encounter a merge conflict, running the git status command shows you which files need to be resolved. For example, if both branches modified the same section of hello.py, you would see something like the following:

```
On branch main
Unmerged paths:
(use "git add/rm ..." as appropriate to mark resolution)
both modified: hello.py
```

##### 충돌을 표시하는 방식
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

##### 요약
1. Git 병합은 커밋 시퀀스를 하나의 통합된 커밋 기록으로 결합합니다.
2. Git을 병합하는 두 가지 주요 방법은 빨리 감기 병합과 3방향 병합입니다.
3. 두 커밋 시퀀스에서 충돌하는 변경 사항이 없으면 Git은 커밋을 자동으로 병합할 수 있습니다.


#### Merge conflicts

![image](https://github.com/user-attachments/assets/9e1e2786-f538-45a4-a21e-bfbdd179b803)


### Reference
Git을 공부할 땐 Git 공식문서도 좋지만, Atlassian 공식문서가 정리를 잘해놔서 큰 도움이 됩니다.
- [Atlassian 공식문서](https://www.atlassian.com/ko/git/tutorials/what-is-git)
