***인프런 '스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술'***

### 1장. 강의 소개

- 스프링 기술을 어떻게 사용하는지, 그리고 왜 사용하는지를 배우는 데 초점
- 스프링 프로젝트 생성 → 스프링 부트로 웹 서버 실행 → 회원 도메인 개발 → 웹 MVC 개발 → DB 연동 → 테스트 케이스 작성


### 2장. 프로젝트 환경설정

#### 1. 프로젝트 생성

스프링 이니셜라이저([start.spring.io](https://start.spring.io))에서 스프링 부트 기반 프로젝트를 생성

| 항목 | 설정 값 | 설명 |
| --- | --- | --- |
| **Project** | Gradle | 라이브러리를 가져오고 빌드 라이프사이클을 관리하는 도구 |
| **Language** | Java | 사용하는 언어 |
| **Group** | `hello` | 기업명 및 도메인 식별자 |
| **Artifact** | `hello-spring` | 빌드 결과물 및 프로젝트명 |
| **Dependencies** | Spring Web, Thymeleaf | 프로젝트에서 사용할 라이브러리 |

Dependencies 탭에서 프로젝트에 끌어올 라이브러리를 설정 가능

#### 2. 프로젝트 구조 확인

생성한 프로젝트를 열면 `src` 폴더가 기본적으로 `main`과 `test`로 나누어져 있다.

- `src/main`: 자바 소스(`java`)와 리소스(`resources`)
- `src/test`: 테스트 코드
- `build.gradle`: 버전과 라이브러리를 가져와 주는 빌드 설정 파일. `dependencies`에는 프로젝트 생성 시 설정한 라이브러리가 있고, 이를 어디서 다운받을지도 규정해 둔다.

메인 클래스를 실행하면 내장된 웹 서버인 톰캣(Tomcat)이 자체적으로 실행되면서 스프링 부트가 올라가고, `localhost:8080`으로 접근 가능

#### 3. 라이브러리 살펴보기

프로젝트 생성 당시 Dependencies로 선택한 라이브러리는 Spring Web과 Thymeleaf뿐이지만, External Libraries를 보면 수많은 라이브러리가 들어와 있기 때문에 라이브러리 간 의존 관계를 잘 알아 둘 필요가 있다.

**핵심 라이브러리**
- `spring-boot-starter-web`
  - `spring-boot-starter-tomcat`: 웹 서버
  - `spring-webmvc`: 스프링 웹 MVC
- `spring-boot-starter-thymeleaf`: 타임리프 템플릿 엔진(View)
- `spring-boot-starter`(공통): 아래 라이브러리들이 함께 들어온다.
  - `spring-boot`
  - `spring-core`
  - `spring-boot-starter-logging`: logback, slf4j

#### 4. View 환경설정

**Welcome Page 만들기**

`src/main/resources/static`에 `index.html`을 만든다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Hello</title>
</head>
<body>
Hello
<a href="/hello">hello</a>
</body>
</html>
```

- 스프링 부트는 여러 기능을 제공하는데, 원하는 기능의 사용법은 spring.io → projects → Spring Boot → Learn → Reference Documentation에서 서칭 가능
- Thymeleaf 같은 템플릿 엔진을 사용하면 Welcome Page도 원하는 대로 꾸밀 수 있다.

**컨트롤러와 뷰 리졸버**

`hello.hellospring.controller` 패키지에 `HelloController.java`를 작성한다.

```java
package hello.hellospring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HelloController {

    @GetMapping("hello")   // GET 메서드, /hello 요청 매핑
    public String hello(Model model) {
        model.addAttribute("data", "Hello!");   // key: data, value: Hello!
        return "hello";   // resources/templates/hello.html로 이동
    }
}
```

- `resources/templates/hello.html`도 함께 만듦. 
- Thymeleaf는 웹에서 `/hello` 호출이 들어오면 컨트롤러가 `hello`를 반환하고, 같은 이름의 `templates/hello.html`을 찾아 처리해 줌. 
- 이때 뷰 리졸버(`viewResolver`)가 화면을 찾아 처리하며, 기본 viewName 매핑은 아래과 같다.

```
resources:templates/ + {ViewName} + .html
```

#### 5. 빌드하고 실행하기

```bash
# 1. 프로젝트 폴더에서 빌드
./gradlew build

# 2. build/libs 폴더로 이동
cd build/libs

# 3. jar 파일 실행
java -jar hello-spring-0.0.1-SNAPSHOT.jar
```

이렇게 빌드해서 실행해도 `localhost:8080`에서 앞서와 똑같이 동작한다.

---

### 3장. 스프링 웹 개발 기초

스프링 웹 개발 방식은 크게 정적 컨텐츠, MVC와 템플릿 엔진, API 3가지로 나뉜다.

#### 1. 정적 컨텐츠

서버에서 별도의 처리 없이 파일을 그대로 클라이언트에게 전달하는 방식. 스프링 부트가 기본으로 제공하며, `resources/static` 안의 파일이 대상이다.

`src/main/resources/static/hello-static.html`

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>static content</title>
</head>
<body>
정적 컨텐츠 입니다.
</body>
</html>
```

`localhost:8080/hello-static.html`로 접근한다.

**동작 원리**
1. 웹 브라우저에서 요청하면 내장 톰캣 서버가 받아 스프링에 넘긴다.
2. 스프링은 컨트롤러에 `hello-static` 매핑이 있는지 먼저 찾는다. (컨트롤러가 우선순위를 가진다.)
3. 매핑된 컨트롤러가 없으면 `resources/static`에서 파일을 찾아 반환한다.

#### 2. MVC와 템플릿 엔진

- MVC: Model, View, Controller로 쪼개어 개발하는 방식. 서버에서 내용을 조금 바꿔서(렌더링해서) 클라이언트에게 전달한다. 
- View: 화면을 그리는 데 집중한다.
- Controller, Model: 내부 처리와 구현에 집중한다.

**Controller**

```java
@Controller
public class HelloController {

    @GetMapping("hello-mvc")
    public String helloMvc(@RequestParam("name") String name, Model model) {
        model.addAttribute("name", name);
        return "hello-template";
    }
}
```

**View** (`resources/templates/hello-template.html`)

```html
<html xmlns:th="http://www.thymeleaf.org">
<body>
<p th:text="'hello ' + ${name}">hello! empty</p>
</body>
</html>
```

`@RequestParam`의 인자는 `localhost:8080/hello-mvc?name=spring`처럼 쿼리 파라미터로 넘긴다. 그러면 모델에 전달된 `name` 값으로 `${name}` 부분이 치환되어 출력된다.

**동작 원리**
1. 웹 브라우저가 요청을 보내면 내장 톰캣 서버를 거쳐 스프링에 전달된다.
2. 매핑된 `HelloController`가 호출되고, 모델에 데이터를 담아 View 이름을 반환한다.
3. `viewResolver`가 반환된 이름과 매핑되는 템플릿을 찾아 템플릿 엔진에 처리를 넘긴다.
4. 템플릿 엔진이 변환한 HTML이 웹 브라우저로 반환된다.

#### 3. API

- JSON 포맷으로 데이터를 클라이언트에게 바로 내려주는 방식. 
- 서버끼리 통신할 때 주로 쓰고, HTML로 내릴지 API로 데이터를 바로 내릴지에 따라 선택한다. 
-`@ResponseBody`는 HTTP 응답의 Body 부분에 반환 값을 직접 넣겠다는 의미

**문자열 반환**

```java
@GetMapping("hello-string")
@ResponseBody
public String helloString(@RequestParam("name") String name) {
    return "hello " + name;
}
```

- MVC 방식과 화면 결과는 같아보이지만, 페이지 소스를 보면 HTML 태그 없이 반환한 문자만 그대로 있다. 이때 `StringHttpMessageConverter`가 동작한다.

**객체(JSON) 반환**

```java
@GetMapping("hello-api")
@ResponseBody
public Hello helloApi(@RequestParam("name") String name) {
    Hello hello = new Hello();
    hello.setName(name);
    return hello;
}

static class Hello {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

- 객체를 반환하면 key-value 구조의 JSON으로 만들어 반환(JSON은 구조가 단순해서 쓰기 좋음)
-`get`과 `set`으로 값을 다루는 방식을 프로퍼티 방식이라고 함.

**@ResponseBody 동작 원리**
1. `@ResponseBody`가 사용되면 `viewResolver` 대신 `HttpMessageConverter`가 동작한다.
2. 반환 타입이 문자이면 `StringHttpMessageConverter`, 객체이면 `MappingJackson2HttpMessageConverter`가 작동한다.
3. 클라이언트의 HTTP Accept 헤더와 서버 컨트롤러의 반환 타입 정보를 조합해서 어떤 Converter를 쓸지 선택한다.
