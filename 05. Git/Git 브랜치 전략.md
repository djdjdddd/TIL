# Git 브랜치 전략

## 1. 베타 버전
![image](https://github.com/djdjdddd/TIL/assets/126077503/59b29e9d-180a-4500-92ae-2436b061215c)

### 설계 포인트
#### 0. 브랜치 구조
- 기존 SVN을 사용했을 때와 동일하게 `개발/검증/운영` 크게 3개 서버(브랜치)로 구성
- 단, `개발`과 `검증/운영`을 서로 분리
  - `개발` 서버에 테스트하고 싶은 코드 및 기능을 마음껏 올릴 수 있음 **(SVN과의 차이점)**

#### 1. `main -> feat` : new branch from main
- 신규 개발 건이 생길 경우 `main` 브랜치로부터 `feat/기능명` 이름으로 새 브랜치를 생성

#### 2. `feat -> dev` : merge (cherry-pick)
- `merge` 및 `cherry-pick`을 이용하여 개발 서버(dev 브랜치)에 올림
  - `merge` 또는 `cherry-pick`은 상황에 맞게 각자 자유롭게 사용

#### 3. `feat -> qlf` : PR
- 반영 내역서 역할
- 이슈 관리
- 코멘트를 통한 소통

#### 4. `main -> dev` : 소스 최신화 (from main)
2개 방법 중 택 1
- `rebase`
- 새 `dev` 브랜치 생성
