_코딩 자율학습 '스프링 부트 3 자바 백엔드 개발 입문'_

## 1장. 스프링 부트 시작하기

### 1.1 스프링 부트(Spring Boot)란?

자바 웹 프로그램을 더욱 쉽고 빠르게 만들기 위한 도구이다. 기존 스프링 프레임워크를 개선하여 개발자가 비즈니스 로직 개발에만 집중할 수 있도록 돕는다.

* **개발 환경 설정 간소화:** 프로젝트에 필요한 외부 라이브러리를 최적화하여 제공한다.
* **웹 애플리케이션 서버 내장:** 톰캣(Tomcat)을 내장하고 있어 별도의 서버 설정 없이 `jar` 파일로 간편하게 배포할 수 있다.

### 1.2 스프링 부트 개발 환경 설정하기

1. **JDK 설치:** 자바 코드의 번역과 실행을 담당하는 개발 도구이다. 스프링 부트 3는 **JDK 17 이상**을 지원한다.
2. **IDE 준비:** 통합 개발 환경으로 주로 인텔리제이(IntelliJ IDEA)를 사용한다.
3. **스프링 부트 프로젝트 생성:** Spring Initializr를 통해 기본 설정 후 프로젝트를 내려받는다.
* **기본 설정:** Project(`Gradle - Groovy`), Language(`Java`), Spring Boot(`3.1.0` 또는 GA 버전), Packaging(`Jar`), Java(`17`), Artifact(`firstproject`)
* **Dependencies(의존성 추가):**
* `Spring Web`: 웹 애플리케이션 개발 도구
* `H2 Database`: 자료 저장을 위한 경량 데이터베이스
* `Mustache`: 화면(뷰 템플릿)을 만들기 위한 도구
* `Spring Data JPA`: 데이터베이스를 편리하게 다룰 수 있도록 돕는 도구





**프로젝트 구조 및 실행**

* `src/main/java/com/example/firstproject/FirstprojectApplication.java`: 프로그램의 시작점인 `main()` 메서드가 위치한 기본 클래스이다.
* `src/main/resources`: 정적 파일, 뷰 템플릿, 설정 파일 등 외부 파일을 저장한다.
* `main()` 메서드를 실행해 콘솔에 `Started FirstprojectApplication` 메시지가 출력되면 서버 구동이 완료된 것이다.

**정적 웹 페이지 출력하기 (`src/main/resources/static/hello.html`)**

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>헬로 월드!</h1>
</body>
</html>

```

* `src/main/resources/static`: 정적 HTML 파일을 위치시키는 기본 경로이다.
* 서버 재시작 후 브라우저에서 `localhost:8080/hello.html`로 접속하면 작성한 화면이 출력된다.

### 1.3 웹 서비스의 동작 원리 이해하기

웹 서비스는 **클라이언트(웹 브라우저)의 요청**에 따른 **서버(스프링 부트)의 응답**으로 동작하며, 응답을 위해서는 반드시 서버가 실행 중이어야 한다.

* **`localhost`:** 실행 중인 서버 주소 중 '내 컴퓨터'를 의미한다. IP 주소로는 `127.0.0.1`에 해당한다.
* **`8080`:** 포트 번호(방 번호 개념)를 의미한다. 스프링 부트의 내장 웹 서버인 톰캣(Tomcat)이 기본적으로 `8080`번 포트에서 수행된다.
* **`hello.html`:** 서버에 요청하는 파일명이다. 스프링 부트는 기본적으로 `static` 디렉터리에서 해당 파일을 찾아 응답으로 전송한다.

---

## 2장. MVC 패턴 이해와 실습

### 2.1 뷰 템플릿과 MVC 패턴

* **뷰 템플릿(View Template):** 화면을 담당하는 기술로, 웹 페이지(View)를 하나의 틀(Template)로 만들고 여기에 변수를 삽입해 사용자마다 다른 페이지를 보여준다. (본 실습에서는 `Mustache` 사용)
* **MVC 패턴:** 웹 페이지를 화면에 보여주고(**View**), 클라이언트의 요청을 받아 처리하고(**Controller**), 데이터를 관리하는(**Model**) 역할을 나누어 개발하는 기법이다.

### 2.2 MVC 패턴을 활용해 뷰 템플릿 페이지 만들기

**1. 뷰 템플릿 페이지 (`src/main/resources/templates/greetings.mustache`)**

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <h1>{{username}}님, 반갑습니다!</h1>
</body>
</html>

```

* `src/main/resources/templates`: 뷰 템플릿 파일을 생성하는 기본 디렉터리이다.
* `{{변수명}}`: 머스테치 문법을 사용해 모델에서 전달받은 변수를 화면에 삽입한다.

**2. 컨트롤러 및 모델 (`src/main/java/com/example/firstproject/controller/FirstController.java`)**

```java
package com.example.firstproject.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class FirstController {

    @GetMapping("/hi")
    public String niceToMeetYou(Model model) {
        model.addAttribute("username", "홍팍");
        return "greetings";
    }
}

```

* `@Controller`: 해당 클래스가 클라이언트의 요청을 받는 컨트롤러임을 선언한다.
* `@GetMapping("/hi")`: `localhost:8080/hi` 경로로 들어오는 GET 요청과 메서드를 매핑한다.
* `Model model`: 뷰 템플릿에서 사용할 데이터를 관리하고 전달하기 위해 매개변수로 받아온다.
* `model.addAttribute("username", "홍팍")`: 템플릿에서 사용할 변수명(`username`)과 변숫값(`"홍팍"`)을 모델에 등록한다.
* `return "greetings"`: 서버가 `templates` 디렉터리에서 `greetings.mustache` 파일을 찾아 웹 브라우저로 전송한다.

### 2.3 MVC의 역할과 실행 흐름 이해하기

클라이언트가 `localhost:8080/hi` 또는 `/bye`를 요청했을 때의 서버 내부 동작 흐름은 다음과 같다.

1. 컨트롤러(`@Controller`)가 클라이언트의 URL 요청(`@GetMapping`)을 접수한다.
2. 매핑된 메서드가 실행되며 데이터를 담을 `Model` 객체를 매개변수로 가져온다.
3. `model.addAttribute()`를 통해 뷰 템플릿에 전달할 변수를 등록한다.
4. 메서드 반환값(파일명)에 해당하는 뷰 템플릿(`.mustache`)을 `templates` 디렉터리에서 찾아, 모델의 변숫값을 적용한 최종 HTML 페이지를 클라이언트에 응답한다.

**`/bye` 페이지 추가 구현 (`FirstController.java` & `goodbye.mustache`)**

```java
// FirstController.java 내부 추가
@GetMapping("/bye")
public String seeYouNext(Model model) {
    model.addAttribute("nickname", "홍길동");
    return "goodbye";
}

```

* `templates/goodbye.mustache` 파일에 `<h1>{{nickname}}님, 다음에 또 만나요!</h1>`를 작성하여 `/bye` 요청 시 동적으로 닉네임이 출력되도록 연결한다.

### 2.4 뷰 템플릿 페이지에 레이아웃 적용하기

* **레이아웃(Layout):** 화면에 요소를 배치하는 일로, 상단의 헤더(Header, 내비게이션)와 하단의 **푸터(Footer, 사이트 정보)** 사이에 핵심 내용인 콘텐츠(Content)를 배치하는 **헤더-푸터 레이아웃**이 가장 기본이 된다.
* **부트스트랩(Bootstrap):** 웹 페이지를 쉽게 디자인할 수 있도록 미리 구현해 놓은 HTML, CSS, JS 코드 모음이다.

**레이아웃 템플릿화 (`src/main/resources/templates/layouts/`)**
여러 페이지에서 헤더와 푸터 코드를 중복 작성하는 비효율을 줄이기 위해 공통 영역을 별도 파일로 분리한다.

* `layouts/header.mustache`: 부트스트랩 스타터 템플릿의 상단 영역과 내비게이션 바(`navbar`) 코드를 분리해 저장한다.
* `layouts/footer.mustache`: 하단 사이트 정보 및 부트스트랩 JS 스크립트 영역을 분리해 저장한다.

**템플릿이 적용된 뷰 페이지 (`src/main/resources/templates/goodbye.mustache`)**

```html
{{>layouts/header}}

<div class="bg-dark text-white p-5">
    <h1>{{nickname}}님, 다음에 또 만나요!</h1>
</div>

{{>layouts/footer}}

```

* `{{>파일경로/파일명}}`: 머스테치 문법을 사용해 분리해 둔 레이아웃 템플릿(`header`, `footer`)을 불러온다.
* 수정된 HTML/Mustache 코드는 서버를 완전히 재시작하지 않고 인텔리제이의 **빌드(망치 아이콘)** 기능만 실행해도 빠르게 반영된다.
