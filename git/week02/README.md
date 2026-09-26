**『Do it! 5일 만에 끝내는 깃&깃허브 입문』 (이지스퍼블리싱)**

## 5장. 깃허브로 협업하기 (p.165~202)

### 1. 원격 저장소 복제하기 — `git clone`

원격 저장소의 내용을 내 컴퓨터의 지역 저장소로 **통째로 가져오는 것**을 복제(clone)라고 한다.

```bash
git clone 원격저장소주소 .
```

* `git clone`: 원격 저장소를 복제
* 마지막 `.`: 현재 디렉터리에 복제한다는 의미

> **협업 시 주의사항**
>
> 여러 컴퓨터에서 하나의 원격 저장소를 함께 사용한다면, 작업을 시작하기 전에 `git pull`을 실행하여 **최신 변경 사항을 먼저 받아오는 것**이 좋다.

### 2. 원격 저장소의 변경 사항 가져오기 — `git fetch`

`git pull`은 기본적으로 **`git fetch` + `git merge`** 과정으로 생각할 수 있다.

```bash
git fetch
```

* 원격 저장소의 최신 변경 사항을 가져옴
* 현재 작업 중인 지역 브랜치에 자동으로 합치지는 않음

따라서 다른 사람이 작업한 내용을 먼저 확인한 후 병합하고 싶을 때 유용하다.

```bash
git fetch
git diff HEAD origin/main
git merge origin/main
```

* `git fetch`: 원격 저장소의 변경 사항 가져오기
* `git diff HEAD origin/main`: 현재 상태와 원격 `main`의 차이 확인
* `git merge origin/main`: 확인 후 원격 변경 사항을 현재 브랜치에 병합

### 3. `git pull`과 `git fetch`의 차이

| 명령          | 기능                              |
| ----------- | ------------------------------- |
| `git fetch` | 원격 변경 사항만 가져오고 현재 브랜치에는 적용하지 않음 |
| `git pull`  | 원격 변경 사항을 가져온 후 현재 브랜치에 병합      |

즉,

```text
git fetch → 확인 → merge
git pull  → fetch + merge
```

### 4. 협업의 기본 흐름

깃허브에서 여러 사람이 하나의 프로젝트를 함께 개발할 때는 일반적으로 다음과 같은 흐름으로 작업한다.

```text
저장소 생성
   ↓
팀원 초대
   ↓
이슈 등록 및 담당자 지정
   ↓
브랜치 생성
   ↓
코드 작성
   ↓
commit → push
   ↓
Pull Request 생성
   ↓
코드 리뷰
   ↓
main 브랜치에 merge
```

#### ① 팀원 초대

GitHub 저장소에서

`Settings → Collaborators → Add people`

을 통해 팀원을 초대할 수 있다.

초대받은 사람이 초대를 수락하면 저장소에 함께 접근할 수 있다.

#### ② 이슈(Issue)

프로젝트에서 해결해야 할 작업이나 버그 등을 **Issue**로 등록할 수 있다.

예를 들어,

```text
Issue #12
로그인 기능 구현
```

처럼 작업 내용을 등록하고 담당자를 지정할 수 있다.

#### ③ 브랜치 생성

각자 맡은 기능을 별도의 브랜치에서 작업한다.

```bash
git switch -c login
```

예를 들어,

```text
main
 ├── login
 ├── signup
 └── payment
```

처럼 기능별로 작업을 분리할 수 있다.

#### ④ 작업 후 커밋 및 푸시

```bash
git add .
git commit -m "로그인 기능 구현"
git push -u origin login
```

처음 원격에 브랜치를 올릴 때는 `-u` 옵션을 사용하여 지역 브랜치와 원격 브랜치를 연결할 수 있다.

```bash
git push -u origin 브랜치명
```

이후에는 간단하게

```bash
git push
```

만 입력해도 된다.

### 5. Pull Request(PR)

**Pull Request**는 작업한 브랜치의 내용을 다른 브랜치, 일반적으로 `main`에 **병합해 달라고 요청하는 것**이다.

```text
작업 브랜치
   ↓
commit
   ↓
push
   ↓
Pull Request
   ↓
Code Review
   ↓
Merge
```

PR에서는 주로 다음과 같은 내용을 확인한다.

* `Conversation`: PR에 대한 설명 및 의견
* `Files changed`: 실제 코드 변경 내용
* 코드 리뷰 및 수정 요청
* 최종적으로 `Merge pull request`

> **PR의 핵심**
>
> PR은 단순히 코드를 올리는 것이 아니라, **다른 개발자가 코드를 검토한 후 main 브랜치에 반영하기 위한 협업 과정**이다.

### 6. 트래킹(Tracking)

지역 브랜치와 원격 브랜치를 연결하는 것을 **트래킹**이라고 한다.

예를 들어,

```text
local login
      ↕
origin/login
```

처럼 서로 연결되어 있으면 `git push`, `git pull`을 간단하게 사용할 수 있다.

```bash
git push -u origin login
```

처음 푸시하면서 트래킹 관계를 설정할 수 있다.

---

## 6장. 깃허브에서 다른 사람과 소통하기 (p.203~226)

### 1. 깃허브 프로필 관리

GitHub에서는 개인 프로필을 통해 자신의 개발 활동을 보여줄 수 있다.

`Settings → Public profile`

에서 다음과 같은 정보를 설정할 수 있다.

* 프로필 사진
* 이름
* 소개
* 소속
* 위치
* 개인 웹사이트

특히 자신의 프로젝트 저장소와 GitHub 활동을 정리해 두면 개발 포트폴리오로 활용할 수 있다.

### 2. 컨트리뷰션 그래프 — 잔디밭

GitHub 프로필에는 **Contribution Graph**가 표시된다.

커밋, Pull Request, Issue 등의 활동이 날짜별로 표시되며 **잔디밭**이라고도 부른다.

### 3. README.md

GitHub 저장소의 첫 화면에서 프로젝트를 설명하는 문서가 **README.md**이다.

README에는 일반적으로 다음과 같은 내용을 작성한다.

```text
프로젝트 소개
주요 기능
사용 기술
실행 방법
프로젝트 구조
스크린샷
트러블슈팅
팀원 및 역할
```

특히 개인 프로젝트를 포트폴리오로 활용할 경우 README를 통해 **무엇을 만들었고, 어떤 기술을 사용했으며, 어떤 문제를 해결했는지** 보여줄 수 있다.

### 4. Markdown 문법

README.md는 **Markdown** 문법으로 작성한다.

| 기능       | 문법          | 설명        |
| -------- | ----------- | --------- |
| 제목       | `# 제목`      | 1단계 제목    |
| 소제목      | `## 제목`     | 2단계 제목    |
| 구분선      | `---`       | 가로 구분선    |
| 순서 있는 목록 | `1. 항목`     | 숫자 목록     |
| 순서 없는 목록 | `- 항목`      | 글머리 목록    |
| 굵게       | `**텍스트**`   | **굵게 표시** |
| 기울임      | `*텍스트*`     | *기울임 표시*  |
| 취소선      | `~~텍스트~~`   | ~~취소선~~   |
| 인라인 코드   | `` `코드` ``  | 한 줄 코드    |
| 코드 블록    | ` ``` `     | 여러 줄 코드   |
| 링크       | `[텍스트](주소)` | 하이퍼링크     |
| 이미지      | `![설명](주소)` | 이미지 삽입    |

예를 들어,

```markdown
# Arduino RC Car

## 프로젝트 소개

Arduino를 활용하여 장애물을 감지하고 회피하는 RC카를 제작했습니다.

## 주요 기능

- 초음파 센서를 이용한 장애물 감지
- 서보모터를 이용한 방향 탐색
- 모터 드라이버를 이용한 주행 제어
```

### 5. 오픈소스 프로젝트에 기여하기

GitHub에서는 다른 사람이 공개한 오픈소스 프로젝트에 직접 기여할 수 있다.

대표적인 방법은 다음과 같다.

#### ① 번역

오픈소스 문서나 README를 다른 언어로 번역하여 기여할 수 있다.

```text
README.md
README.ko-KR.md
```

#### ② Issue 등록

버그나 오타를 발견했다면 `Issues → New issue`를 통해 문제를 제보할 수 있다.

#### ③ 코드 수정 및 Pull Request

직접 버그를 수정하거나 새로운 기능을 구현한 후 Pull Request를 제출할 수도 있다.

```text
Fork
 ↓
Clone
 ↓
수정
 ↓
Commit
 ↓
Push
 ↓
Pull Request
```

---

## 7장. VS Code에서 GUI 방식으로 사용하기 (p.227~249)

VS Code에서는 터미널 명령어를 직접 입력하지 않고 **Source Control(소스 제어)** 기능을 이용하여 Git을 GUI 방식으로 사용할 수 있다.

### 1. Git 명령어와 VS Code GUI 비교

| Git 명령어                | VS Code GUI             |
| ---------------------- | ----------------------- |
| `git init`             | 소스 제어 → 리포지토리 초기화       |
| `git add`              | 파일 옆 `+` 클릭             |
| `git commit -m`        | 커밋 메시지 입력 → `✓` 클릭      |
| `git diff`             | 변경된 파일 클릭               |
| `git restore --staged` | 파일 옆 `-` 클릭             |
| `git restore`          | `↩` 클릭                  |
| `git reset HEAD^`      | `…` → 커밋 → 마지막 커밋 실행 취소 |

### 2. VS Code에서 파일 상태 확인

VS Code의 Source Control 창에서는 파일 옆에 상태를 나타내는 문자가 표시된다.

| 표시  | 의미                       |
| --- | ------------------------ |
| `U` | Untracked — 추적하지 않는 새 파일 |
| `M` | Modified — 기존 파일이 수정됨    |
| `A` | Added — 스테이징 영역에 추가됨     |

### 3. VS Code에서 브랜치 관리

화면 하단의 현재 브랜치 이름을 클릭하면 브랜치를 생성하거나 다른 브랜치로 전환할 수 있다.

```text
main
 ├── feature-login
 ├── feature-signup
 └── feature-payment
```

### 4. 원격 저장소 연결

VS Code의 Git 메뉴에서 원격 저장소를 추가할 수도 있다.

```text
… → 원격 → 원격 추가
```

또는 터미널에서 직접 연결할 수도 있다.

```bash
git remote add origin 원격저장소주소
```

### 5. Pull / Push

VS Code에서는 Source Control의 메뉴나 하단 상태 표시줄의 **동기화 아이콘**을 이용하여 원격 저장소와 동기화할 수 있다.

```text
Pull → 원격 변경 사항 가져오기
Push → 내 변경 사항 원격에 올리기
Sync → Pull + Push
```

### 6. 브랜치 병합 및 삭제

`main` 브랜치로 이동한 후 병합할 브랜치를 선택한다.

```bash
git switch main
git merge 브랜치명
```

병합이 완료된 브랜치는 필요하지 않다면 삭제할 수 있다.

```bash
git branch -d 브랜치명
```

---

## 8장. 깃허브에 이력서 사이트와 블로그 만들기 (p.250~275)

### 1. GitHub Pages

**GitHub Pages**를 이용하면 GitHub 저장소에 있는 웹 페이지를 인터넷에서 공개할 수 있다.

HTML, CSS, JavaScript로 만든 개인 홈페이지나 포트폴리오 사이트를 배포하는 데 활용할 수 있다.

### 2. 이력서 사이트 만들기

기존 이력서 템플릿을 활용하는 경우 일반적인 과정은 다음과 같다.

```text
Fork
 ↓
Clone
 ↓
HTML/CSS 수정
 ↓
Commit
 ↓
Push
 ↓
GitHub Pages 설정
 ↓
웹사이트 배포
```

#### ① Fork

다른 사람의 저장소에서 `Fork`를 선택하여 자신의 GitHub 계정으로 복사한다.

#### ② Clone

Fork한 저장소를 자신의 컴퓨터로 가져온다.

```bash
git clone 저장소주소
```

#### ③ 수정

주로 다음과 같은 파일을 수정한다.

```text
index.html
style.css
```

`index.html`은 웹사이트의 메인 페이지 역할을 한다.

#### ④ GitHub Pages 설정

GitHub 저장소에서

`Settings → Pages`

로 이동한 뒤 배포할 Branch를 선택한다.

예:

```text
Branch: main
Folder: /(root)
```

설정이 완료되면 GitHub Pages를 통해 웹사이트가 공개된다.

일반적인 주소 형태는 다음과 같다.

```text
https://아이디.github.io/저장소명
```

### 3. Jekyll 기반 블로그

GitHub Pages에서는 **Jekyll**을 이용해 블로그를 만들 수도 있다.

사용자 계정의 GitHub Pages 사이트를 만드는 경우 저장소 이름을 다음과 같이 지정한다.

```text
계정.github.io
```

예:

```text
funbooki.github.io
```

### 4. `_config.yml`

Jekyll 블로그의 기본 설정은 `_config.yml`에서 관리한다.

```yaml
name: 이름
description: 블로그 설명
```

이 파일에서 블로그 이름, 설명, 소셜 링크 등의 정보를 수정할 수 있다.

### 5. 포스트 작성

Jekyll에서는 `_posts` 디렉터리에 글을 작성한다.

파일 이름은 다음과 같은 형식을 사용한다.

```text
YYYY-MM-DD-제목.md
```

예:

```text
2026-09-26-git-github-study.md
```

### 6. Front Matter

Jekyll 포스트에는 문서의 정보를 지정하는 **Front Matter**가 필요하다.

```yaml
---
layout: post
title: Git과 GitHub 정리
---
```

Front Matter 아래에 Markdown으로 본문을 작성한다.

```markdown
---
layout: post
title: Git과 GitHub 정리
---

## Git이란?

Git은 버전 관리 시스템이다.
```

### 7. 이미지 삽입

이미지를 `images` 디렉터리에 저장한 뒤 Markdown 문법으로 삽입할 수 있다.

```markdown
![이미지 설명](/images/파일명.png)
```

---

## 총정리

### 원격 저장소 및 협업

```bash
# 원격 저장소 복제
git clone 원격저장소주소 .

# 원격 변경 사항 가져오기
git fetch

# 원격 변경 사항과 현재 상태 비교
git diff HEAD origin/main

# 원격 변경 사항 병합
git merge origin/main

# 원격 저장소와 동기화
git pull
git push

# 새 브랜치 최초 푸시
git push -u origin 브랜치명
```

### 협업 기본 흐름

```text
Issue 생성
 ↓
Branch 생성
 ↓
코드 작성
 ↓
Commit
 ↓
Push
 ↓
Pull Request
 ↓
Code Review
 ↓
Merge
```

### GitHub 핵심 개념

| 개념             | 의미                             |
| -------------- | ------------------------------ |
| `clone`        | 원격 저장소를 내 컴퓨터로 복제              |
| `fetch`        | 원격 변경 사항만 가져오기                 |
| `pull`         | 원격 변경 사항을 가져와 현재 브랜치에 반영       |
| `push`         | 내 커밋을 원격 저장소에 올리기              |
| `PR`           | 브랜치의 변경 사항을 다른 브랜치에 병합해 달라고 요청 |
| `Fork`         | 다른 사람의 저장소를 내 계정으로 복사          |
| `Issue`        | 작업·버그·기능 등을 관리하는 공간            |
| `Tracking`     | 지역 브랜치와 원격 브랜치를 연결             |
| `GitHub Pages` | GitHub 저장소를 이용한 웹사이트 호스팅       |

### VS Code Git 핵심

```text
U → Untracked
M → Modified
A → Added

+  → Stage
✓  → Commit
↩  → Restore
-  → Unstage
🔄 → Sync
```

### Git 전체 흐름 한눈에 보기

```text
[작업 트리]
     │
     │ git add
     ↓
[스테이지]
     │
     │ git commit
     ↓
[지역 저장소]
     │
     │ git push
     ↓
[원격 저장소 GitHub]
     │
     │ git pull
     ↓
[지역 저장소]
```

### 협업 흐름 한눈에 보기

```text
GitHub Repository
       │
       ├── main
       │
       ├── feature-A
       │       ↓
       │    작업 → commit → push
       │       ↓
       │    Pull Request
       │       ↓
       │    Code Review
       │       ↓
       └──── Merge → main
```

> **한 줄 정리**
>
> Git은 버전 관리 도구이고, GitHub는 Git 저장소를 기반으로 다른 사람과 코드를 공유하고 협업할 수 있는 플랫폼이다.
