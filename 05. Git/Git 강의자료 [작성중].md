- [Git merge,rebase,cherry-pick 설명 + 그림 자료](https://hqjang.tistory.com/114)
- [그림 참조용](https://spl.hevs.io/spl-docs/tools/git/general.html)
- [아틀라시안 공식자](https://www.atlassian.com/git/tutorials/saving-changes/git-stash)
- [6 Git Cherry-Pick Commands You Should Know](https://www.hatica.io/blog/git-cherry-pick-commands/)
  - 체리픽에 대해 이해하기 좋은 자료
  - https://www.geeksforgeeks.org/git-cherry-pick/

---

## 1. Git Workflow
![image](https://github.com/user-attachments/assets/752796e5-2cb8-4166-8cbe-a5edcd8caa37)

### Reference
- https://www.linkedin.com/in/brijpandeyji/
- ㅇㄹ

---

## 2. Git Commit Message Convention

### 메시지 구조 및 예시
#### 기본 포맷
```
type: Subject

body

footer
```

#### 예시
```
Feat: 사용자 프로필 페이지 추가

- 사용자 프로필 페이지 및 라우팅 구현
- 프로필 정보를 보여주는 프로필 카드 컴포넌트 구현
- 프로필 수정 기능 구현

Closes #123
```

### WRMS에서 채택한 방식
```
#이슈번호 [이름] type: Subject

body (필수X)

footer (필수X)
```

#### 예시
```
#123 [홍길동] Feat: xxx 기능 추가
```

### Type 종류
`Feat`부터 `Chore`까지 주로 7가지를 사용하며, 경우에 따라 `Comment/Rename/Remove 등` 더 세분화하여 사용하는 경우도 있습니다.
- `Feat` : 새로운 기능 추가
- `Fix` : 버그 수정
- `Docs` : 문서 수정
- `Style` : 코드 포맷팅, 세미콜론 누락 (production code와 무관한 경우)
- `Refactor` : 코드 리팩토링
- `Test` : 테스트 코드, 리팩토링 테스트 코드 (production code와 무관한 경우)
- `Chore` : 빌드 수정, 패키지 매니저 설정 등 (production code와 무관한 경우)
  - `build.gradle`이나 `.gitignore`처럼 설정 코드를 변경하는 경우에 해당
- `Comment` : 주석 추가 및 변경
- `Rename` : 파일, 폴더명 수정
- `Remove` : 파일, 폴더 삭제

### Reference
- [Udacity Git Commit Message Style Guide](https://udacity.github.io/git-styleguide/)
- [요즘IT - Git 커밋 메시지 컨벤션은 왜 중요할까?](https://yozm.wishket.com/magazine/detail/1974/)
- [InfoGrab - Git 커밋 메시지는 왜 중요할까?](https://insight.infograb.net/blog/2023/04/21/why-commit-convention-is-important/)

---

## 3. Git Command 익히기 (자주 사용하는 명령어에 대해서만 강의)
### Saving changes (Git add)
#### git commit
#### git stash

### Undoing changes
#### git revert
#### git reset

### Syncing (git remote)
#### git fetch
#### git push
#### git pull

### Using Branches (Git branch)
#### git checkout
#### git merge
![image](https://github.com/user-attachments/assets/d4de665d-876c-4c0c-affc-dd6c4ef20998)

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

---

## 4. What is Git (맨 처음에 보여줄 부분 & 요약 필요)
### Git이란
지금까지 오늘날 환경에서 가장 널리 사용되는 최신 버전 제어 시스템은 Git입니다. Git은 원래 Linux 운영 체제 커널을 만든 사람으로 유명한 Linus Torvalds가 2005년에 개발한 활발하게 유지 관리되는 성숙한 오픈 소스 프로젝트입니다. 상용 프로젝트와 오픈 소스를 포함한 수많은 소프트웨어 프로젝트가 버전 제어에 Git을 사용하고 있습니다. Git을 사용해본 적이 있는 개발자 중에는 유능한 소프트웨어 개발 인재가 많으며 Git은 다양한 운영 체제와 IDE(통합 개발 환경)에서 잘 작동합니다.

![image](https://github.com/user-attachments/assets/8bc36839-6893-47fa-9a66-5ce70fc39792)

분산 아키텍처를 사용하는 Git은 DVCS(분산된 버전 제어 시스템)의 한 예입니다. 한때 CVS나 Subversion(SVN이라고도 함)과 같이 널리 사용되었던 버전 제어 시스템에서 흔히 볼 수 있듯이 소프트웨어의 전체 버전 기록을 한 곳에 보관하는 것이 아니라 Git에서는 모든 개발자의 코드 작업 복사본 역시 모든 변경 사항의 전체 기록을 담을 수 있는 리포지토리입니다.

Git은 분산 외에도 성능, 보안, 유연성을 염두에 두고 설계되었습니다.

### 성능
Git의 원시 성능 특성은 다른 여러 도구에 비해 매우 강력합니다. 새로운 변경 사항 커밋, 브랜칭, 병합 및 이전 버전 비교는 모두 성능에 최적화되어 있습니다. Git에 구현된 알고리즘은 실제 소스 코드 파일 트리의 일반적인 특성, 시간이 지남에 따라 수정되는 일반적인 방식 및 액세스 패턴에 대한 심층적인 지식을 활용합니다.

일부 버전 제어 소프트웨어와는 달리 Git은 파일 트리의 스토리지와 버전 기록을 결정할 때 파일 이름에 속지 않으며 그 대신 파일 콘텐츠 자체에 중점을 둡니다. 결국 소스 코드 파일은 이름이 변경되고, 분할되며, 다시 배열되는 경우가 많습니다. Git 리포지토리 파일의 개체 형식은 델타 인코딩(콘텐츠 차이 저장)과 압축의 조합을 사용하며 디렉터리 콘텐츠와 버전 메타 데이터 개체를 명시적으로 저장합니다.

분산은 성능 면에서 상당한 이점을 제공합니다.

예를 들어, 개발자인 Alice가 곧 제공될 2.0 릴리스에 기능을 추가하면서 소스 코드를 변경한 다음 설명 메시지와 함께 변경 사항을 커밋한다고 가정해 보겠습니다. 그런 다음 두 번째 기능을 작업하고 해당 변경 사항도 커밋합니다. 이 두 가지 변경 사항은 버전 기록에 별도의 작업으로 저장됩니다. Alice는 이전 버전에만 영향을 미치는 버그를 수정하기 위해 같은 소프트웨어의 버전 1.3 브랜치로 전환합니다. 그 목적은 Alice의 팀이 버전 2.0이 준비되기 전에 버그 수정 릴리스 버전 1.3.1을 제공할 수 있도록 하기 위해서입니다. 그러면 Alice는 2.0 브랜치로 돌아가 버전 2.0의 새로운 기능에 대한 작업을 계속할 수 있으며 네트워크에 액세스하지 않고도 모든 작업을 수행할 수 있기 때문에 빠르고 안정적으로 이루어집니다. 심지어는 비행기에서도 작업이 가능합니다. Alice는 개별적으로 커밋된 모든 변경 사항을 원격 리포지토리로 보낼 준비가 되면 하나의 명령으로 "푸시"할 수 있습니다.

### 보안
Git은 관리형 소스 코드의 무결성을 최우선으로 하여 설계됐습니다. 파일의 콘텐츠뿐만 아니라 파일과 디렉터리, 버전, 태그와 커밋 간의 실질적인 관계, Git 리포지토리의 모든 개체는 SHA1이라는 암호학적으로 안전한 해싱 알고리즘으로 보호됩니다. 이를 통해 우발적이거나 악의적인 변경으로부터 코드와 변경 기록을 보호하고 기록을 완전히 추적할 수 있습니다.

Git을 사용하면 소스 코드의 확실한 콘텐츠 기록을 가질 수 있습니다.

일부 다른 버전 제어 시스템에는 나중에 이루어지는 비밀스러운 변경에 대한 보호 기능이 없습니다. 이것은 소프트웨어 개발에 의존하는 모든 조직에 심각한 정보 보안 취약점이 될 수 있습니다.

### 유연성
Git의 주요 설계 목표 중 하나는 유연성입니다. Git은 여러 측면에서 유연합니다. 다양한 유형의 선형이 아닌 개발 워크플로를 지원하고 소규모 및 대규모 프로젝트에서 효율적이며 많은 기존 시스템 및 프로토콜과 호환됩니다.

Git은 SVN과 달리 최첨단 도구로서 브랜칭 및 태그를 지원하도록 설계되었으며 브랜치와 태그에 영향을 미치는 작업(예: 병합 또는 되돌리기)도 변경 기록의 일부로 저장됩니다. 모든 버전 제어 시스템에 이러한 수준의 추적 기능이 있는 것은 아닙니다.

### 버전 제어 및 Git
오늘날 대부분의 소프트웨어 팀에게는 Git이 최고의 선택입니다. 팀마다 다르고 팀 자체적으로 분석해야 하지만 Git을 사용한 버전 제어가 다른 도구보다 선호되는 주된 이유는 다음과 같습니다.

Git은 훌륭합니다
Git은 대부분의 팀과 개별 개발자가 필요로 하는 기능, 성능, 보안, 유연성을 갖추고 있습니다. Git의 이러한 특성은 위에 자세히 설명되어 있습니다. 대부분의 다른 도구와 나란히 비교했을 때 많은 팀에서 Git을 선호하고 있습니다.

Git은 사실상의 표준입니다
Git은 해당 유형의 도구 중에서 가장 광범위하게 채택되는 도구입니다. 이것은 다음과 같은 이유로 Git을 매력적으로 만듭니다. Atlassian에서는 거의 모든 프로젝트 소스 코드를 Git에서 관리됩니다.

이미 Git 사용 경험이 있는 개발자들이 많으며 대학 졸업자 중 상당수는 Git만 사용해본 경험이 있습니다. 일부 조직은 다른 버전 제어 시스템에서 Git으로 마이그레이션할 때 학습 곡선을 거쳐야 할 수도 있지만, 기존과 미래의 개발자 중 다수는 Git에 대한 교육을 받을 필요가 없습니다.

대규모 인재 풀의 이점 외에도 Git이 우세하다는 것은 IDE를 포함한 많은 타사 소프트웨어 도구 및 서비스와 DVCS 데스크톱 클라이언트인 Sourcetree, 이슈 및 프로젝트 추적 소프트웨어인 Jira, 코드 호스팅 서비스인 Bitbucket과 같은 자체 도구가 이미 Git에 통합되어 있다는 의미입니다.

소프트웨어 개발 도구에서 중요한 기술을 쌓고 싶어하는 초급 개발자라면 버전 제어에 있어서 Git을 고려하는 것이 좋을 것입니다.

Git은 높은 품질의 오픈 소스 프로젝트입니다
Git은 10년 넘게 확고한 관리를 제공한 매우 잘 지원되는 오픈 소스 프로젝트입니다. 프로젝트 관리자는 사용성과 기능성을 개선하는 정기 릴리스를 통해 사용자의 장기적인 요구 사항을 충족하기 위한 균형 잡힌 판단력과 성숙한 접근 방식을 보여주었습니다. 오픈 소스 소프트웨어의 품질에 대해 쉽게 조사가 이루어지고 그 품질에 크게 의존하는 기업이 아주 많습니다.

Git은 훌륭한 커뮤니티 지원과 방대한 사용자층을 가지고 있습니다. 책, 자습서, 전용 웹사이트를 포함한 여러 유용한 설명서와 팟캐스트 및 동영상 자습서도 있습니다.

오픈 소스이기 때문에 취미로 개발하는 개발자들은 비용을 지불하지 않고 Git을 사용할 수 있으므로 비용이 절감됩니다. 오픈 소스 프로젝트에서 사용할 수 있는 Git은 의심할 여지 없이 이전 세대의 성공적인 오픈 소스 버전 제어 시스템인 SVN과 CVS의 후속 도구입니다.

Git에 대한 비판
Git에 대한 일반적인 비판 중 하나는 배우기 어려울 수 있다는 것입니다. Git에서 사용하는 용어 중 일부는 처음 접하는 사용자에게 생소할 것이며 다른 시스템의 사용자에게는 Git 용어가 다를 수 있습니다. 예를 들어, Git에서 revert는 SVN 또는 CVS에서와 다른 의미를 갖습니다. 그럼에도 불구하고 Git은 뛰어난 기능을 자랑하며 사용자에게 많은 능력을 제공합니다. 그 능력을 활용하는 방법을 배우려면 시간이 좀 걸릴 수 있지만 배운 후에는 팀이 그 능력을 사용하여 개발 속도를 높일 수 있습니다.

분산되지 않은 VCS로 작업했던 팀에게 중앙 리포지토리란 잃고 싶지 않은 좋은 것처럼 보일 수 있습니다. 그러나 Git은 분산된 버전 제어 시스템(DVCS)으로 설계되었지만 Git을 사용하면서도 소프트웨어에 대한 모든 변경 사항을 저장해야 하는 공식적인 기본 리포지토리를 계속 유지할 수 있습니다. Git을 사용하면 각 개발자의 리포지토리가 완전하기 때문에 작업이 "중앙" 서버의 가용성과 성능에 제약을 받을 필요가 없습니다. 서비스가 중단되었거나 오프라인 상태일 때에도 개발자들은 전체 프로젝트 기록을 계속해서 확인할 수 있습니다. Git은 유연하고 분산되어 있기 때문에 익숙한 방식으로 작업하면서도 Git의 추가적인 이점을 얻을 수 있으며 어떤 이점은 놓치고 있다는 사실조차 깨닫지 못할 수도 있습니다.

버전 제어와 Git이 무엇인지, 소프트웨어 팀이 Git을 사용해야 하는 이유를 알아봤으므로 Git이 조직 전체에 제공할 수 있는 이점을 알아보려면 계속 읽어보세요.
