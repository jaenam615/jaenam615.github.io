---
title: "디자인 패턴 - 싱글톤, 팩토리 메서드, 전략 패턴"
writer: James
date: 2024-10-24 15:55:01 +0900
categories: [cs, design pattern]
tags: [cs, design pattern]
pin: false
math: true
mermaid: true
image:
  path: https://contents.kyobobook.co.kr/sih/fit-in/458x0/pdt/9788945072146.jpg
---

## 디자인 패턴 

디자인 패턴이란, 프로그램 설계에서 자주 발생하는 문제를 해결하기 위한 일반적인 솔루션을 제공한다. 객체 간의 상호 관계 등을 이용하여 해결할 수 있도록 하나의 '규약'형태로 만들어 놓은 것이 곧 디자인 패턴이다.  

객체지향적 디자인 패턴의 카테고리는 크게 '생성 패턴', '구조 패턴', '행동 패턴'으로 구분된다.  

> 생성 패턴  
>> 객체 생성 방식에 관련된 패턴으로, 객체를 만드는 과정의 복잡성을 줄여준다. 

> 구조 패턴
>> 객체와 클래스의 조합을 다루며, 객체들 간의 관계를 정의하여 더 큰 구조를 형성하는데 도움을 준다.  

> 행동 패턴
>> 객체 간의 상호작용과 책임 분배를 다루며, 객체가 어떻게 협력하여 특정 작업을 수행하는지를 정의한다.  

## 싱글톤 패턴  

싱글톤 패턴은 하나의 클래스가 오직 하나의 인스턴스만 가지는 패턴이다. 이렇게 하나의 인스턴스만 만들어 해당 인스턴스를 다른 모듈들이 공유하며 사용함으로 인스턴스를 생성할 때 드는 비용이 줄어들지만, 의존성이 높아진다.  

> 장점: 인스턴스 생성 비용 감소, 다른 클래스 간에 데이터 공유가 쉬움 
> 단점: 의존성 높아짐, 동시성 문제 발생 가능 

앞서 디자인 패턴은 자주 발생하는 문제의 해결에 대한 일반적인 솔루션을 제공한다고 한다. 그러면 싱글톤 패턴 전에는 어떤 문제가 자주 발생했을까?  

싱글톤 패턴은 데이터베이스 연결 모듈에 많이 쓰인다. 데이터베이스 연결은 자원 소모가 큰 작업으로, 매번 새로운 연결을 생성하면 메모리와 성능에 부담을 준다. 싱글톤 패턴을 사용함으로 단 하나의 데이터베이스 연결 인스턴스를 생성하고 재사용함으로 자원을 효율적으로 관리할 수 있다.  

즉, 싱글톤 패턴은 객체의 중복 생성으로 인해 발생한 불필요한 메모리 소모 및 성능 저하의 문제를 해결하며, 여러 인스턴스의 생성으로 인한 데이터 불일치 문제를 해결한다.  

다만, 싱글톤 패턴은 단위 테스팅을 할 때 걸림돌이 된다. 단위 테스트는 테스트가 서로 독립적이어야 하며, 순서에 영향 없이 실행할 수 있어야 하지만 싱글톤 패턴은 미리 생성된 하나의 인스턴스를 기반으로 구현하는 패턴이므로 각 테스트마다 독립적인 인스턴스 생성이 어렵다.  


## 팩토리 메서드 패턴  

팩토리 패턴은 객체를 사용하는 코드에서 객체 생성 부분을 떼어내 추상화한 패턴으로, 상속 관계에 있는 두 클래스에서 상위 클래스에서 뼈대를 결정하고 하위 클래스에서는 구체적인 내용을 결정하는 패턴이다.  

일단 자세히 알아보기 전에 내가 생각하는 상위 클래스와 하위 클래스의 개념을 자주 사용되는 동물의 예시로 표현을 해보자면 다음과 같다:  

```java
class Animal {
    String name;

    public void makeSound(){
        System.out.println("Animal makes a sound");
    } 
}

class Cat extends Animal {
    @Override
    public void makeSound(){
        System.out.println("Cat meows");
    }

    public void jump(){
        System.out.println("Cat jumps");
    }
}                   

class Cow extends Animal {
    @Override
    public void makeSound(){
        System.out.println("Cow moos");
    }

    public void eat(){
        System.out.println("Cow eats");
    }
}  
```

근데, 이런 방식으로는 직접 객체를 `Cat cat = new Cat()`와 같은 방식으로 직접 생성하게 된다. 구체적인 클래스에 의존하여 객체를 생성하게 되면 코드가 특정 클래스에 종속되며, 이후 객체 생성 방식이 변경되면 코드 수정이 많이 발생할 수 있다. 팩토리 패턴은 이러한 구체적인 생성 로직을 캡슐화하여 숨기고, **팩토리 메서드**를 통해 객체를 생성하도록 함으로 코드를 리팩터링하더라도 생성 로직이 구현된 팩토리 메서드 하나만 수정하면 된다.  

팩토리 메서드 패턴의 핵심은, 객체 생성의 책임을 서브클래스가 지니도록 하는 것이다. 그렇게 되면 코드에서 직접적으로 객체를 생성하지 않고, 팩토리 클래스의 메서드를 호출하여 생성하게 된다. 

```java
abstract class Animal {
    protected String name;
    protected String sound;

    public Animal(String name, String sound){
        this.name = name;
        this.sound = sound;
    }

    public void makeSound(){
        System.out.println("Makes sound " + this.sound);
    } 
}

class Cat extends Animal{
    public Cat(){
        super("Cat", "meow");
    }

    public void jump(){
        System.out.println("Cat jumps");
    }
}

class Cow extends Animal {

    public Cow(){
        super("Cow", "moo");
    }
    public void eat(){
        System.out.println("Cow eats");
    }
}

class AnimalFactory {
    public static Animal checkAnimal(String type){
        if("Cat".equalsIgnoreCase(type)) return new Cat();
        else if ("Cow".equalsIgnoreCase(type)) return new Cow();
        return null;
    }    
}
```

이 팩토리 메서드 패턴이라는 게 이해하기가 어렵다.  

이해한 부분을 말해보자면, `Animal`이라는 상위 추상 클래스를 만들어 주요 뼈대와 공통 기능을 만들고, 하위 클래스인 `Cat`과 `Cow`가 구체적인 내용을 결정한다. 이렇게 만들어진 클래스들인 `Cat`과 `Cow`를 직접 클라이언트 코드에서 `new`로 생성하는 것이 아닌, `Animal` 종류를 만드는 공장인 `AnimalFactory`를 만들어서 이를 통해 생성한다.  

아래와 같이 Main 클래스를 구현하면 `Cat`객체를 생성하고 어떤 소리를 내는지 확인할 수 있다.  

```java
public class Main {
    public static void main(String[] args){
        Animal animal = AnimalFactory.checkAnimal("Cat");
        animal.makeSound();
    }
}
```

이렇게 팩토리 메서드 패턴을 사용하면 추후 makeSound의 로직이 변경되어도 여러 클라이언트 코드에서 직접 수정하지 않고 하나의 `Animal`클래스 메서드만 수정하면 된다는 이점이 있어 코드의 유지보수성이 높아진다.  

## 전략 패턴 

객체의 행위를 바꾸고 싶은 경우 객체를 직접 수정하지 않고 '전략'이라고 부르는 '캡슐화한 알고리즘'을 컨텍스트 안에서 바꿔주면서 상호 교체가 가능하게 만드는 패턴이다.  

무언가를 살 때 어떤 결제 방식으로 사는지, 특정 사이트에 로그인 할 때 어떤 방식으로 로그인하는지 등이 전략 패턴을 사용하여 구현할 수 있다. 다음은 *'면접을 위한 CS 전공지식 노트'*에서 사용된 예제이다:  

```java
interface PaymentStrategy {
    public void pay(int amount)
}

class KAKAOCardStrategy implements PaymentStrategy {
    private String name;
    private String cardNumber;
    private String cvv;
    private String dateOfExpiry;

    public KAKAOCardStrategy (String nm, String ccNum, String cvv, String expiryDate){
        this.name = nm;
        this.cardNumber = ccNum;
        this.cvv = cvv;
        this.dateOfExpiry = expiryDate;
    }

    @Override
    public void pay(int amount){
        System.out.println(amount + " paid using KAKAO Card");
    }
}

class LUNACardStrategy implements PaymentStrategy {
    private String emailId;
    private String password;

    public LUNACardStrategy(String email, String pw){
        this.emailId = email;
        this.password = pw;
    }

    @Override
    public void pay(int amount){
        System.out.println(amount + " paid using LUNA Card");
    }
}
```

위처럼 인터페이스에 대해 카드 종류로 나누어진 구현체 둘이 있다고 가정해보자.  

```java
class ShoppingCart {
    // 상품을 담는 코드 
    // 상품을 카트에서 없애는 코드 
    
    public int calculateTotal(){
        int sum = 0;
        for (Item item : items){
            sum += item.getPrice();
        }
    }

    public void pay(PaymentStrategy paymentMethod){
        int amount = calculateTotal();
        paymentMethod.pay(amount);
    }
}
```
쇼핑 카트 클래스와 그 내부에 카트 안에 담긴 상품을 일괄 결제하는 메서드 있으면, 해당 메서드는 인자로 결제방식 클래스를 받도록 되어있다.  

```java
public class paymentLesson {
    public static void main(String[] args){
        ShoppingCart cart = new ShoppingCart();

        Item A = new Item("1A", 100);
        Item B = new Item("2B", 300);

        cart.addItem(A);
        cart.addItem(B);

        //pay by LUNA
        cart.pay(new LUNACardStrategy("j@example.com", "j123456"));

        //출력: 400 paid using LUNA Card

        //pay by KAKAO
        cart.pay(new KAKAOCardStrategy("Jae", "123456789", "123", "12/01"));

        //출력: 400 paid using KAKAO Card
    }
}
```

위와 같이 전략 자체를 `LUNACardStrategy`와 `KAKAOCardStrategy`간 교체해서 끼워넣기만 하면 사용된 클래스의 `pay`메서드가 실행된다.  

즉, `ShoppingCart`클래스의 행위 자체는 변경하되 직접 수정을 하고 싶지 않은 경우 `LUNACardStrategy`와 `KAKAOCardStrategy` 내부에 캡슐화되어 구현된 알고리즘을 `ShoppingCart` 컨텍스트 안에서 바꿔주는 것이다. 이런 방식을 채택하면 구현체의 변경이 클라이언트 코드에 영향을 미치지 않게 되어 유지보수에 용이하며 코드의 유연성을 높일 수 있다.   

## 정리  

**싱글톤 패턴**
- 하나의 클래스 객체에 대해 단일 인스턴스만 생성하여 메모리 공간을 절약
- 전역 접근성을 보장해주며, 이로 인해 코드의 일관성 유지

**팩토리 메서드 패턴**
- 클라이언트 코드에서 직접 객체를 생성하지 않고 팩토리 클래스의 메서드를 사용하여 객체 생성
- 객체 생성 로직을 분리 및 캡슐화하여 유지보수성 향상  
  
**전략 패턴**
- 어떤 알고리즘을 선택할 지 런타임 중 선택이 가능해지기 때문에 코드 변경 없이도 교체 가능 
- 캡슐화하여 책임을 분리하여 각 모듈의 코드 응집도 향상
- 구체적인 알고리즘보다는 전략 인터페이스에 의존하게 되어 코드 간 결합도 감소