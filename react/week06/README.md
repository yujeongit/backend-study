***인프런 '스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술'***

### 4장. 회원 도메인과 리포지토리 개발

#### 1. 비즈니스 요구사항 정리

* 데이터: 회원 ID, 이름
* 기능: 회원 등록, 회원 조회
* DB 저장소: 아직 확정되지 않음 (가장 단순한 메모리 기반 저장소로 개발 진행 후 추후 교체)

애플리케이션 계층 구조

* 컨트롤러 (Controller): 웹 MVC의 컨트롤러 역할. 비즈니스 로직과 관련 있거나 서버 내부적 처리 담당.
* 서비스 (Service): 핵심 비즈니스 로직 구현 (예: 중복 회원 가입 방지 등).
* 리포지토리 (Repository): 데이터베이스에 접근하여 도메인 객체를 DB에 저장, 관리.
* 도메인 (Domain): 비즈니스 도메인 객체 (예: 회원, 주문, 쿠폰 등 데이터베이스에 저장되고 관리됨).

---

#### 2. 회원 도메인과 리포지토리 만들기

회원 도메인 객체 (`src/main/java/hello/hellospring/domain/Member.java`)
회원 객체는 도메인 패키지에서 관리하므로 domain 패키지에 Member 클래스를 생성한다.

```java
package hello.hellospring.domain;

public class Member {

    private Long id;
    private String name;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

```

회원 리포지토리 인터페이스 (`src/main/java/hello/hellospring/repository/MemberRepository.java`)
아직 데이터 저장소가 확정되지 않았으므로 인터페이스로 역할을 먼저 설계하여 구현 클래스를 변경할 수 있도록 구조화한다.

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;

import java.util.List;
import java.util.Optional;

public interface MemberRepository {
    Member save(Member member);
    Optional<Member> findById(Long id);
    Optional<Member> findByName(String name);
    List<Member> findAll();
}

```

* 인터페이스(Interface): 모든 멤버가 추상 메소드로 구성되며, 객체 생성(new)이 불가능하다. 다중 상속(구현)이 가능하며, 공통된 기능 구현을 강제하는 설계도 역할을 한다.
* Optional: 자바 8에 도입된 개념으로 null이 될 수 있는 객체를 감싸는 Wrapper 클래스이다. NPE(NullPointerException)를 안전하게 방지한다.

메모리 회원 리포지토리 구현체 (`src/main/java/hello/hellospring/repository/MemoryMemberRepository.java`)
초기 개발 단계에서 가볍게 사용할 메모리 기반 데이터 저장소 구현체이다.

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import java.util.*;

public class MemoryMemberRepository implements MemberRepository {

    // 동시성 문제가 고려되어 있지 않음, 실무에서는 ConcurrentHashMap, AtomicLong 사용 고려
    private static Map<Long, Member> store = new HashMap<>();
    private static long sequence = 0L;

    @Override
    public Member save(Member member) {
        member.setId(++sequence);
        store.put(member.getId(), member);
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        return Optional.ofNullable(store.get(id));
    }

    @Override
    public Optional<Member> findByName(String name) {
        return store.values().stream()
                .filter(member -> member.getName().equals(name))
                .findAny(); // 조건에 맞는 요소를 찾으면 Optional로 감싸서 반환
    }

    @Override
    public List<Member> findAll() {
        return new ArrayList<>(store.values());
    }

    public void clearStore() {
        store.clear();
    }
}

```

* 오버라이딩 (Overriding): 상속받은/인터페이스의 메소드 내용을 재정의하는 기술. (매개변수 타입과 개수, 리턴 타입이 동일해야 함)
* 자바 스트림 (Stream): .stream()으로 데이터를 생성하고 .filter()로 조건 검증 후 .findAny()로 결과를 반환하는 유연한 병렬 가공 패턴을 제공한다.

---

#### 3. 회원 리포지토리 테스트 케이스 작성

main 메소드 실행이나 웹 서버 구동 없이 개발 코드를 빠르고 반복적으로 검증하기 위해 자바 단위 테스트 프레임워크인 JUnit과 AssertJ를 사용한다.

테스트 클래스 (`src/test/java/hello/hellospring/repository/MemoryMemberRepositoryTest.java`)

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.Test;

import java.util.List;

import static org.assertj.core.api.Assertions.*;

public class MemoryMemberRepositoryTest {

    MemoryMemberRepository repository = new MemoryMemberRepository();

    // 각 테스트 메소드 실행이 끝날 때마다 호출되는 콜백 함수
    @AfterEach
    public void afterEach() {
        repository.clearStore(); // 저장소를 삭제하여 테스트 간 데이터 의존성을 제거
    }

    @Test
    public void save() {
        // given
        Member member = new Member();
        member.setName("spring");

        // when
        repository.save(member);

        // then
        Member result = repository.findById(member.getId()).get();
        // Assertions.assertEquals(member, result); // JUnit 방식
        assertThat(member).isEqualTo(result); // AssertJ 체이닝 방식
    }

    @Test
    public void findByName() {
        // given
        Member member1 = new Member();
        member1.setName("Spring1");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("Spring2");
        repository.save(member2);

        // when
        Member result = repository.findByName("Spring1").get();

        // then
        assertThat(result).isEqualTo(member1);
    }

    @Test
    public void findAll() {
        // given
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("spring2");
        repository.save(member2);

        // when
        List<Member> result = repository.findAll();

        // then
        assertThat(result.size()).isEqualTo(2);
    }
}

```

* Given-When-Then 패턴: 주어진 상황(given) -> 실행(when) -> 결과 검증(then)의 직관적인 테스트 작성 구조.
* @AfterEach: 한 번에 여러 테스트를 실행할 때 이전 테스트 결과가 남아서 테스트에 영향을 주는 일을 방지한다. 테스트 케이스는 독립적으로 실행되는 것이 원칙이다.
* AssertJ assertThat(): 메소드 체이닝 패턴(assertThat().isEqualTo())을 지원하여 단정문 가독성을 크게 높여준다.

---

#### 4. 회원 서비스 개발 및 테스트

회원 서비스 클래스 (`src/main/java/hello/hellospring/service/MemberService.java`)
리포지토리가 기계적인 데이터 접근 위주라면, 서비스 클래스는 비즈니스 용어를 사용하며 핵심 로직(예: 중복 회원 검증)을 처리

```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;

import java.util.List;
import java.util.Optional;

public class MemberService {

    private final MemberRepository memberRepository;

    // 외부에서 의존관계를 주입받도록 설계 (DI - Dependency Injection)
    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    /**
     * 회원 가입
     */
    public Long join(Member member) {
        validateDuplicateMember(member); // 중복 회원 검증
        memberRepository.save(member);
        return member.getId();
    }

    private void validateDuplicateMember(Member member) {
        memberRepository.findByName(member.getName())
                .ifPresent(m -> {
                    throw new IllegalStateException("이미 존재하는 회원입니다.");
                });
    }

    /**
     * 전체 회원 조회
     */
    public List<Member> findMembers() {
        return memberRepository.findAll();
    }

    public Optional<Member> findOne(Long memberId) {
        return memberRepository.findById(memberId);
    }
}

```

회원 서비스 테스트 클래스 (`src/test/java/hello/hellospring/service/MemberServiceTest.java`)

```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemoryMemberRepository;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import static org.assertj.core.api.Assertions.*;
import static org.junit.jupiter.api.Assertions.*;

class MemberServiceTest {

    MemberService memberService;
    MemoryMemberRepository memberRepository;

    @BeforeEach
    public void beforeEach() {
        memberRepository = new MemoryMemberRepository();
        memberService = new MemberService(memberRepository); // 동일한 리포지토리 사용 (DI)
    }

    @AfterEach
    public void afterEach() {
        memberRepository.clearStore();
    }

    @Test
    void 회원가입() {
        // given
        Member member = new Member();
        member.setName("hello");

        // when
        Long saveId = memberService.join(member);

        // then
        Member findMember = memberService.findOne(saveId).get();
        assertThat(member.getName()).isEqualTo(findMember.getName());
    }

    @Test
    public void 중복_회원_예외() {
        // given
        Member member1 = new Member();
        member1.setName("spring");

        Member member2 = new Member();
        member2.setName("spring");

        // when
        memberService.join(member1);
        IllegalStateException e = assertThrows(IllegalStateException.class, 
                () -> memberService.join(member2));

        // then
        assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");
    }
}

```

* @BeforeEach: 각 테스트 실행 직전에 호출되어 새로운 객체와 동일한 리포지토리 의존관계를 맺어준다. 테스트용 객체와 실제 로직의 객체가 상이해지는 문제(인스턴스 파편화)를 해결한다.
* 예외 검증: assertThrows()를 통해 지정한 예외가 발생하는지 깔끔하게 검증

---

### 5장. 스프링 빈과 의존관계

스프링 빈(Spring Bean): 기존 자바처럼 new로 직접 객체를 생성하지 않고, 애플리케이션 실행 시 스프링 컨테이너가 직접 생성하고 관리하는 자바 객체를 의미

---

#### 1. 컴포넌트 스캔과 자동 의존관계 설정

클래스 상단에 컴포넌트 어노테이션을 붙이면 스프링 실행 시 자동으로 스캔하여 스프링 빈으로 등록하고 @Autowired로 연관 객체의 의존성을 자동 주입(DI)한다.

어노테이션 구성

* @Controller: 웹 MVC 컨트롤러 자동 등록
* @Service: 비즈니스 로직 서비스 자동 등록
* @Repository: 데이터 접근 리포지토리 자동 등록
* @Autowired: 스프링 컨테이너가 관리하는 스프링 빈 간의 의존성 자동 주입

컨트롤러 작성 및 의존관계 설정 (`src/main/java/hello/hellospring/controller/MemberController.java`)

```java
package hello.hellospring.controller;

import hello.hellospring.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;

@Controller
public class MemberController {

    private final MemberService memberService;

    @Autowired // 생성자 호출 시 스프링 컨테이너의 MemberService를 찾아 연결 (DI)
    public MemberController(MemberService memberService) {
        this.memberService = memberService;
    }
}

```

서비스 및 리포지토리에 어노테이션 추가

* MemberService 클래스 상단에 @Service, 생성자에 @Autowired 추가
* MemoryMemberRepository 클래스 상단에 @Repository 추가

컴포넌트 스캔 원리 및 싱글톤 스코프

* @Controller, @Service, @Repository 어노테이션 내부에는 @Component가 포함되어 있어 스프링 빈으로 자동 등록된다.
* @SpringBootApplication이 위치한 패키지(hello.hellospring) 및 하위 패키지들이 컴포넌트 스캔의 대상이다.
* 스프링은 빈 등록 시 기본적으로 싱글톤(Singleton)으로 등록하여 객체를 오직 하나만 공유 및 재사용한다.

---

#### 2. 자바 코드로 직접 스프링 빈 등록하기

어노테이션 기반 자동 스캔 대신, 설정 클래스(SpringConfig)를 직접 만들어 스프링 빈을 등록하는 방법

1. MemberService, MemoryMemberRepository에서 @Service, @Repository, @Autowired를 제거 (@Controller는 스프링 웹 관리를 위해 유지)
2. hello.hellospring 위치에 SpringConfig.java 클래스를 만든다.

설정 클래스 작성 (`src/main/java/hello/hellospring/SpringConfig.java`)

```java
package hello.hellospring;

import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SpringConfig {

    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
}

```

- 실무 활용 포인트: 
  - 정형화된 컨트롤러, 서비스, 리포지토리는 보통 컴포넌트 스캔을 사용하지만, 상황에 따라 구현 클래스를 변경해야 하는 구조(예: 아직 DB가 정해지지 않아 추후 DB 구현체로 바꿔 껴야 하는 비정형 상황)에서는 자바 코드로 직접 빈을 등록하는 방식을 사용한다.
  - 비즈니스 로직을 전혀 수정하지 않고 SpringConfig 설정만 변경해 리포지토리 구현체를 바꿀 수 있다.

---

#### 3. 의존성 주입(DI)의 3가지 방식

| 주입 방식 | 코드 형태 | 특징 및 장단점 |
| --- | --- | --- |
| 생성자 주입 (Constructor) | `public MemberController(MemberService ...)` | 객체 생성 시점에 1번 딱 설정되며 불변하게 설계 가능. 가장 권장되는 방식. |
| 필드 주입 (Field) | `@Autowired private MemberService ...` | 외부에서 변경 및 테스트 작성이 힘들어 권장하지 않음. |
| Setter 주입 | `public void setMemberService(...)` | public으로 노출되어 실행 도중 예기치 않게 변경될 위험성이 존재함. |

- 주의
  - @Autowired를 통한 DI는 스프링 컨테이너에 의해 등록되고 관리되는 스프링 빈 객체 사이에서만 동작. 
  - 개발자가 일반 자바 코드로 new를 써서 직접 생성한 객체 내부에서는 동작하지 않는다.
