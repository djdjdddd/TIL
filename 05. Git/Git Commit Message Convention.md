# Git Commit Message Convention

## 목차
- 일반적인 방식
- WRMS에서 채택한 방식
- Type 종류

---

### 1. 일반적인 방식
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

---

### 2. WRMS에서 채택한 방식
```
#이슈번호 [이름] type: Subject

body (필수X)

footer (필수X)
```

#### 예시
```
#123 [홍길동] Feat: xxx 기능 추가
```

---

### 3. Type 종류
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
