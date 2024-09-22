# Git 브랜치 전략

## 목차
1. 실제 운영 버전 (Production version)
2. 초기 구상 단계 (Prototype version)

## 1. 실제 운영 버전 (Production version)
### 1) 브랜치 전략
![image](https://github.com/user-attachments/assets/cbfe01a0-f7cc-4b52-9e8c-770de7703efd)

| **이름**    | **설명**         | **비고**  |
|-------------|------------------|-----------|
| main        | 운영 배포         | 배포 후 `main_yyMMdd` 형태로 백업 브랜치 생성     |
| hotfix      |  긴급 배포        |   -  |
| rls         | 다음 정기 배포 대기 목록  | `rls/yyMMdd` 형태로 생성    |
| qlf         | 검증 배포         | -     |
| dev         | 개발 배포         | -     |
| feat        | 기능 변경 및 추가 개발        | `feat/기능명` 형태로 생성     |
| prj         | 단위 프로젝트 (ex. 고도화)    | 개발 기간이 1~2주 이상 소요될 경우 고려    |

### 2) 세부 전략
#### 2-1) 브랜치 생성
- 개발 진행 전 브랜치 생성은 반드시 `origin/main` 기준으로 생성
- 가시성을 위해 `/`를 사용하여 Tree 구조로 생성
- 가급적 배포될 브랜치만 `push`하여 `Remote`에 브랜치가 쌓이는 걸 방지하고, 주기적으로 브랜치 삭제

#### 2-2) 배포 담당자
- 각 프로젝트마다 1명씩 선정
- 배포 전 `main` 브랜치 백업 &rightarrow; `main_yyMMdd`
- 배포 당일 `rls` 브랜치 생성 &rightarrow; `rls/yyMMdd`
- `dev`, `qlf` 브랜치의 경우 적절한 주기마다 삭제 후 재생성
  - 시간이 지날수록 운영 소스와의 차이가 누적되기 때문에 소스 동기화를 위해 진행
  - 단, 개발자들에게 미리 고지 후 작업할 것

#### 2-3) Pull Request
- 원칙상 `main` 브랜치에 `direct push` 불가. 반드시 `PR` 이용
  - 깃랩 설정에서 해제하거나 특정 역할(Mainatainer)에게 권한을 부여할 수 있으나 권장 X
- `Maintainer` 역할을 가진 담당자가 PR 승인

#### 2-4) Commit message rule
- {이슈번호} {[이름]} 간단한 코멘트 &rightarrow; `#1 [김용희] Feat: Git TIL 작성`
- 이를 위해 이슈 생성이 선행되어야 함

### 레퍼런스
- [인파 - 깃 브랜치 전략 정](https://inpa.tistory.com/entry/GIT-%E2%9A%A1%EF%B8%8F-github-flow-git-flow-%F0%9F%93%88-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EC%A0%84%EB%9E%B5)
- [GitLab의 역할과 권한](https://twoseed.atlassian.net/wiki/spaces/OPS/pages/2142601258/GitLab)
- [인포그랩 GitLab Guide](https://insight.infograb.net/guide/)

---

## 2. 초기 구상 단계 (Prototype version)
### 1) 브랜치 전략
![슬라이드2](https://github.com/djdjdddd/TIL/assets/126077503/bdea9f5d-e864-42a5-8e85-3205f43b74bf)


### 2) 설계 포인트
#### 2-1) 브랜치 구조
- 기존 SVN을 사용했을 때와 동일하게 `개발/검증/운영` 크게 3개 서버(브랜치)로 구성하였습니다.
- 단, `개발계`, `검증계`, `운영계`는 완전히 분리된 상태입니다.

#### 2-2) 장점
- 테스트하고 싶은 소스를 개발`(dev)` 및 검증`(qlf)`에 마음껏 올릴 수 있습니다. **(SVN과의 차이점)**
- 예를 들어, 검증에서만 테스트할 수 있는 인터페이스도 굳이 개발을 거치지 않아도 되기 때문에 훨씬 더 빠르게 개발이 가능합니다.

### 3) git flow 설명 
#### 3-1) Merge or cherry-pick
- 테스트할 소스를 개발 또는 검증 서버에 올릴 때는 `merge`나 `cherry-pick`을 사용합니다.
  - `merge`와 `cherry-pick`은 각자의 상황에 맞춰 자유롭게 사용
- 별도의 `PR` 과정이 없습니다.


#### 3-2) Merge Request (=Pull Request, 약칭 PR)
- 개발 및 테스트가 끝난 `feat` 브랜치를 `release` 브랜치로 병합할 때는 반드시 `PR`을 이용합니다.
- 단순 `merge`가 아니라 `PR`을 통해 병합하는 이유는 다음과 같습니다.
  - 반영 내역서 역할
  - 이슈 관리
  - 코멘트 기능을 통한 설계자와 개발자 사이의 소통
  - 쉽게 말해 기록을 남기고, 코드 리뷰를 하기 위함입니다.

> PR, Merge Request, Pull Request 모두 같은 뜻입니다.

#### 3-3) New branch
`release` 브랜치
- 신규 개발 및 반영 건이 있다면 `main` 브랜치로부터 `release` 브랜치를 생성합니다.
- 명명법 : 배포일자나 이슈번호를 이용합니다. `release/yyyyMMdd` 또는 `release/01`

`feat` 브랜치
- 방금 생성한 `release` 브랜치로부터 만든 브랜치입니다.
- 명명법 : 기능명을 붙입니다. `feat/login` 등

#### 3-4) 소스 최신화 (미정)
2개 방법 중 택 1
- `rebase`
- 새 `dev` 브랜치 생성
