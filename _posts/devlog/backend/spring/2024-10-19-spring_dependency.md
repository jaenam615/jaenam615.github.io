---
title: "스프링 의존성"
writer: James
date: 2024-10-19 14:30:00 +0900
categories: [devlog, backend, spring]
tags: [devlog, backend, spring, java, framework]
pin: false
math: true
mermaid: true
image:
  path: https://blogcodestates.com/wp-content/uploads/2022/11/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8-%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC.png?fit=900%2C675&ssl=1
---

## 의존관계  

서비스의 인스턴스를 만들 때, 해당 서비스는 일반적으로 여러 인스턴스를 만들 필요가 없다. `@Controller`, `@Service`, `@Repository`등과 같은, TypeORM을 사용할 때 썻던 데코레이터를 사용해서 스프링 컨테이너에 해당 클래스들을 등록할 수 있다. `@Autowired`를 생성자에 달아주면 필요한 의존성들을 주입시켜준다.  

이러한 방식이 컴포넌트 스캔 방식이라고 하며, `@Controller`, `@Service`, `@Repository` 들은 `@Component`의 특수한 케이스이다. 

### IoC와 싱글톤  

IoC란 Inversin of Control, 즉 제어의 역전이란 뜻이다. 제어의 역전이란, 기존 사용자가 new 연산을 통해 객체를 생성하고 메서드를 호출하던 것을 스프링이 하도록 하는 것으로 스프링에 의해 관리당하는 자바 객체를 사용한다. 이 때 스프링이 관리하는 객체는 스프링 빈이라고 한다. 위에서 말한 `@Controller`, `@Service`, `@Repository` 등의 `@Component` 데코레이터 변형들을 사용하면 해당 클래스는 스프링 빈에 자동으로 등록된다. 

스프링은 컨테이너에 스프링 빈을 등록할 때 기본으로 싱글톤으로 등록하며 이는 해당 객체를 하나만 등록해서 공유한다는 뜻이다. 즉, 같은 스프링 빈이라면 모두 같은 인스턴스인 것이다.  

## 자바 코드로 직접 스프링 빈 등록  

`@Configuration`을 데코레이터로 갖는 Config 클래스를 만들어준 후 `@Bean`어노테이션을 사용하면 직접 코드로 스프링 빈에 인스턴스를 등록할 수 있다.  

```java
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

--- 

실무에서는 주로 정형화된 컨트롤러, 서비스, 레포지토리와 같은 코드는 컴포넌트 스캔 방식을 사용하며, 정형화되어 있지 않은 경우에는 설정을 통해 스프링 빈으로 등록한다.  

