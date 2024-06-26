## git
### rebase
다른 분들은 모르겠는데 저는 개인적으로 main 혹은 주요 브랜치(ex. develop) 등에서 제가 개발하는 브랜치로 끌어올 때 종종 씁니다.
다만 개발 브랜치에서 주요 브랜치로 병합성 작업할 때는 무조건 merge로 합니다.

rebase 단점
- 해당 커밋 내역이 어느 브랜치에서 왔는지, 어디서 언제 합병됐는지 추적하기가 어렵다. (커밋 내역을 일종의 평탄화 작업을 하기 때문에)
- 결론 : rebase가 최선의 상황인 경우 빼고는 거~의 쓰지 않는다.

### git pull vs git fetch
원칙적으론 fetch를 먼저 하고 pull을 받아야 한다.
1. `git fetch --all`
  - 왜냐하면 fetch를 받아야 remote의 (새로 생성되거나 삭제된) 브랜치 정보를 local에 받아오게 되고

- 예시
  - `git fetch -a` : 리모트 저장소의 모든 걸 가져옴
  - `git switch develop` : 로컬 develop 브랜치로 이동
  - `git merge origin/develop` : 리모트 develop 브랜치에 있는 커밋 내역을 가져옴

![image](https://github.com/djdjdddd/TIL/assets/126077503/e29a8926-0125-4955-ae47-572d20699b08)

2. `git pull (origin main)`
  - 이어서 pull을 받게 되면 remote 정보를 local에 받고 병합시킨다.

#### git pull 할 때 3가지 옵션을 고르라고 하는 경우
- 비트버킷 [Git pull fails with a warning about reconciling divergent branches](https://confluence.atlassian.com/bbkb/git-pull-fails-with-a-warning-about-reconciling-divergent-branches-1271693522.html)
- https://devum.tistory.com/77
- https://kgw7401.tistory.com/103
- https://sanghye.tistory.com/43

### git revert vs git reset
- revert : 테스트 필요
- reset : 사용 X

### git workflow
1. N사 버전
2. 전 직장 버전
