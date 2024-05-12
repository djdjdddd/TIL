# Git 전환 프로젝트 대비 자료

## 목차
1. [Git branch & workflow](#1-git-branch--workflow)
2. [`git remote prune origin` 와 `git fetch --prune`](#2-git-remote-prune-origin-와-git-fetch---prune)
3. [Git, GitLab 공부 ★](#3-git-gitlab-공부)
4. [Migrate from SVN to Git 레퍼런스](#4-migrate-from-svn-to-git-레퍼런스)

---

## 1. Git branch & workflow
### branch 종류 및 사용 예시
- [git 브랜치의 종류 및 사용](https://gmlwjd9405.github.io/2018/05/11/types-of-git-branch.html)
### workflow 종류
- [Feature Branch Workflow](https://gmlwjd9405.github.io/2017/10/27/how-to-collaborate-on-GitHub-1.html) - 소규모 프로젝트에서 많이 사용하는 방식
- [Forking Workflow](https://gmlwjd9405.github.io/2017/10/28/how-to-collaborate-on-GitHub-2.html) - 오픈 소스 프로젝트에서 많이 사용하는 방식
- [Gitflow Workflow](https://gmlwjd9405.github.io/2018/05/12/how-to-collaborate-on-GitHub-3.html) - 대규모 프로젝트에서 사용하는 엄격한 방식

#### 중요한 내용 체크
https://gmlwjd9405.github.io/2017/10/27/how-to-collaborate-on-GitHub-1.html
![image](https://github.com/djdjdddd/TIL/assets/126077503/190524ed-fdb9-402a-bbcd-76ae8027ef5c)

![image](https://github.com/djdjdddd/TIL/assets/126077503/0124b847-a096-4abf-8f85-b1b66da12a1b)

---

## 2. `git remote prune origin` 와 `git fetch --prune`
- [스택오버플로우 - What are the differences between git remote prune, git prune, git fetch --prune, etc](https://stackoverflow.com/questions/20106712/what-are-the-differences-between-git-remote-prune-git-prune-git-fetch-prune)
- [Git prune이란?](https://shbear-coding.tistory.com/17)
### 상황
- `git pull origin feature/sns-login` 해서 리모트의 feature/sns-login 브랜치의 작업물을 가져오려고 했는데
- `refs/remotes/origin/feature/sns-login/google`라는 브랜치가 이미 존재하여 `feature/sns-login` 브랜치를 만들 수 없다고 뜸.
- 그러면서 `git remote prune origin` 명령어를 이용하여 old, conflicting 브랜치들을 제거하라고 함.
- 내가 사용한 `git fetch --prune`와 같은 용도라고 함.

### 콘솔
#### 에러 문구
```
PS C:\Users\USER\IdeaProjects\help-you-move> git pull origin feature/sns-login
From https://github.com/help-you-move/help-you-move
 * branch            feature/sns-login -> FETCH_HEAD
error: cannot lock ref 'refs/remotes/origin/feature/sns-login': 'refs/remotes/origin/feature/sns-login/google' exists; cannot create 'refs/remotes/origin/feature/sns-login'
 ! [new branch]      feature/sns-login -> origin/feature/sns-login  (unable to update local ref)
error: some local refs could not be updated; try running
 'git remote prune origin' to remove any old, conflicting branches
```

#### 해결
- 원격 저장소에서 삭제된 branch를 로컬에서도 삭제

```
PS C:\Users\USER\IdeaProjects\help-you-move> git fetch --prune
From https://github.com/help-you-move/help-you-move
 - [deleted]         (none)     -> origin/feature/sns-login/google
 - [deleted]         (none)     -> origin/feature/sns-login/kakao
 * [new branch]      develop           -> origin/develop
 * [new branch]      feature/sns-login -> origin/feature/sns-login
```


---


## 3. Git, GitLab 공부

### 1) GitHub vs GitLab 차이
- [깃허브 vs. 깃랩, 무엇을 써야할까](https://zdnet.co.kr/view/?no=20220804135812)
```
■ 깃허브와 깃랩의 핵심 차이점 '데브옵스'
깃허브와 깃랩의 가장 큰 차이점은 데브옵스 요소에 있다.
깃랩은 지속적 통합/지속적 전달(CI/CD)와 데브옵스 워크플로우를 내장했다.
반면, 깃허브를 사용하면 사용자가 원하는 CI/CD 도구를 직접 통합해야 한다. 깃허브 사용자는 보통 젠킨스, CircleCI, TravisCI 같은 써드파티 프로그램을 사용한다.

■ 소프트웨어 서비스
두 서비스의 또 다른 차이점은 소프트웨어 서비스의 구성이다.
깃랩은 완전한 소프트웨어 개발 솔루션을 제공하며, 올인원의 데브옵스 플랫폼이라고 강조한다. 깃랩은 지라(Jira), 마이크로소프트 팀즈, 슬랙, G메일 같은 애프리케이션 및 플랫폼과 통합을 제공한다.
깃허브는 자체적으로 깃랩보다 적은 수의 소프트웨어 서비스를 제공한다. 대신 외부 프로그램 및 서비스와 통합하는 쉬운 방법을 제공한다. 깃허브 마켓플레이스에서 다양한 외부 서비스와 프로그램, 깃허브와 통합을 위한 소프트웨어를 이용할 수 있다.
```

- [GitLab과 GitHub, 실무에서 어떤 차이가 있을까요?](https://insight.infograb.net/blog/2021/02/05/gitlab-vs-github/)
  ![image](https://github.com/djdjdddd/TIL/assets/126077503/8ac658db-2df1-4a4c-8b9f-d5570fa0cb54)


### 2) Git 강의
[제대로 파는 Git & GitHub - by 얄코](https://www.inflearn.com/course/%EC%A0%9C%EB%8C%80%EB%A1%9C-%ED%8C%8C%EB%8A%94-%EA%B9%83?inst=a17e4bef#curriculum)

#### 2-1) git revert / git reset : 이전 커밋으로 돌리기 (커밋 롤백)
- https://formulous.tistory.com/11
- https://www.freecodecamp.org/korean/news/giteseo-keomis-doedolrigi-majimag-keomiseul-cwisohaneun-bangbeob/
- Fast-forward 의미 : 과거로 돌아가서~, 되감기
    - https://celine99.tistory.com/201


### 3) GitLab 소개
- [생활코딩](https://opentutorials.org/course/785/4933)

버전관리 시스템(VCS, Version Control System)
- VCS는 소프트웨어를 제작하면서 발생하는 변화들을 기록해서 소프트웨어의 변화를 열람하고, 수정할 수 있도록 도와주는 시스템이다.

Git
- 버전관리 시스템을 구현한 구체적인 제품 중에 하나이다.
- 분산형 VCS((DVCS, Distributed Version Control System))라는 특징을 갖고 있다. <-> SVN은 중앙집중식 VCS(CVCS, Centralized Version Control System)
- 가장 많이 사용하는 버전관리 시스템 중에 하나이다.

원격 저장소
- VCS는 소스코드를 저장할 수 있는 서버를 운영할 수 있다.
- 서버에 소스코드를 저장함으로써 협업, 백업, 프로젝트 홍보 등의 다양한 장점이 생긴다.

이슈 트래커
- issue tracker는 프로젝트를 진행하는 과정에서 발생하는 업무들을 관리해주는 업무 관리 시스템이다.
- 이슈 트래커는 소프트웨어 개발 프로젝트나 다른 협업 작업에서 발생하는 문제점, 기능 요청, 버그, 작업 항목 등을 추적하고 관리하는 도구입니다. 주요 목적은 문제의 발견부터 해결까지의 과정을 추적하고, 팀원들 간의 커뮤니케이션을 돕고, 프로젝트 관리를 효율적으로 지원하는 것입니다.
- 종류
    - GitLab Issues
    - Jira
    - 기타 등등

GitLab
- Git의 원격 저장소 기능과 이슈 트래커 기능 등을 제공하는 소프트웨어입니다.
- `설치형 Github`라는 컨셉으로 시작된 프로젝트이기 때문에 Github과 비슷한 면이 많습니다.
- 서비스형 원격저장소를 운영하는 것에 대한 비용이 부담되거나 **소스코드의 보안이 중요한 프로젝트**에 적합합니다.

참고
- Gitlab 소개 - http://gitlab.org
- Gitlab 소스코드 - https://github.com/gitlabhq
- Gitlab 클라우드 - http://www.gitlab.com/


### 4) Merge Request 관련 꿀팁 (by 강운 매니저님)
- `Merge Request(=깃허브의 Pull Request)`할 때 `Changes`에서 코드에 코멘트 달아줄 수 있다.
- 칸반 보드 내장하고 있음 ㄷㄷ
- `NaverCommerceToken`


### 5) ★ GitLab 세팅에 큰 도움됐던 레퍼런스들
- [gitlab 그룹 생성 및 프로젝트를 그룹에 포함 시키기](https://www.lesstif.com/gitbook/gitlab-27984328.html)
- ★[인텔리제이 VCS 메뉴 재설정하기 (Git 또는 SVN 으로 변경)](https://jong-bae.tistory.com/57)
- ★[gitignore 생성 사이트](https://www.toptal.com/developers/gitignore)


### 6) ★ Push an existing folder
깃랩에 깡통 레포를 만들어놓고, 기존재하는 폴더를 올리고 싶은 경우
```git
git init --initial-branch=main
git remote add origin [repository URL]
git add .
git commit -m 'Initial Commit'
git push --set-upstream origin main
```

![image](https://github.com/djdjdddd/TIL/assets/126077503/292a316a-5c41-407b-97c9-cbe505440259)


### 7) ★ GitLab 용어, 세팅 익히기
- `Protected branches`를 설정하던 도중 `Developers`는 알겠는데, `Maintainers`는 뭐지?? https://insight.infograb.net/docs/user/members_permissions/
- ★★★인포그랩에 나와있는 `멤버와 권한 관리` 설명 그대로 가이드 제작해도 되겠는데??
  ![image](https://github.com/djdjdddd/TIL/assets/126077503/7cbe66e1-f8b8-49a0-83d5-34a0472ca4c0)


---


## 4. Migrate from SVN to Git 레퍼런스
- SVN을 Git으로 Migration 하기 : https://garve32.tistory.com/43
- 다나와 기술블로그 : https://danawalab.github.io/common/2020/02/07/Common-SvnToGit-Migration.html
- 마이크로소프트 : https://learn.microsoft.com/en-us/azure/devops/repos/git/perform-migration-from-svn-to-git?view=azure-devops
- 링크드인 : https://www.linkedin.com/pulse/migrating-from-svn-git-8-steps-preserving-history-giovanni-zito/
- 깃크라켄 : https://www.gitkraken.com/blog/migrating-git-svn
- 아틀라시안 : https://www.atlassian.com/git/tutorials/migrating-overview
