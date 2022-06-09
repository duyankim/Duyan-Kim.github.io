---
layout: post
author: "Yan"
catalog: true
subtitle: "\<HeadFirst Design Pattern\>을 읽고"
header-img: "img/header/none4.jpg"
title: "Gof Design Pattern - 01. 전략패턴"
date: 2022-06-08 23:15:08 +0000
categories:
  - DesignPattern
tags:
  - 디자인패턴
  - Gof
  - 전략패턴
comments: true
---

# ✏️ (01장) 전략패턴

> 오리 시뮬레이션 게임을 만들고 있다고 가정한다.
## 1. `Duck` 슈퍼 클래스를  상속받은 다양한 유형의 오리 만들기

```java
public class Duck {
  public void quack() {
    // 모든 오리가 소리를 낼 수 있다.
  }

  public void swim() {
    // 모든 오리가 수영할 수 있다.
  }

  public abstract void display(); 
  // 모든 오리의 모양이 다르기 때문에 추상클래스로 구성

  // 기타 등등의 오리 관련 메소드
}
```

```java
public class MallardDuck extends Duck {
  @Override
  public void display() {
    // 모양을 나타냄
  }
}
```

```java
public class RedheadDuck extends Duck {
  @Override
  public void display() {
    // 모양을 나타냄
  }
}
```

### 추상 메소드 
- 자식 클래스에서 반드시 오버라이딩해야만 사용할 수 있는 메소드를 의미한다.
- 모듈처럼 중복되는 부분이나 공통적인 부분은 미리 다 만들어진 것을 사용하고, 이를 받아 사용하는 쪽에서는 자신에게 필요한 부분만을 재정의하여 사용함으로써 생산성이 향상되고 배포 등이 쉬워진다.
- 선언부만 존재. 구현부는 작성되어 있지 않다.

####  자바에서 추상 메소드를 선언하여 사용하는 목적
- 추상 메소드가 포함된 클래스를 상속받는 **자식 클래스가 반드시 추상 메소드를 구현하도록 하기 위함**
- 만약 일반 메소드로 구현한다면 사용자에 따라 해당 메소드를 구현할 수도 있고, 안 할 수도 있다.
- 하지만 추상 메소드가 포함된 추상 클래스를 상속받은 모든 자식 클래스는 추상 메소드를 구현해야만 인스턴스를 생성할 수 있다.

## 2. 상속을 생각하여 오리에게 fly() 메소드 추가하기

아래와 같이 Duck 슈퍼 클래스에 fly() 메소드를 넣으면 코드를 재사용할 수 있지만, 유지보수에 용이하지 않다.

```java
public class Duck {
  public void quack() { }

  public void swim() { }

  public abstract void display(); 

  public void fly() { }

  // 기타 등등의 오리 관련 메소드
}
```

만약, 날지 못하는 고무 오리가 Duck 슈퍼 클래스를 상속받으면, fly() 메소드가 아무것도 하지 않도록 오버라이드 해야 한다.  

### Duck의 행동을 상속하면 생기는 단점들
- 서브클래스에서 코드가 중복된다.
- 실행 시에 특징을 바꾸기 힘들다.
- 모든 오리의 행동을 알기 힘들다.
- 코드를 변경했을 때 다른 오리들에게 원치 않은 영향을 끼칠 수 있다.

## 3. 디자인 원칙 적용하기

### 📘 디자인 원칙 1
> 애플리케이션에서 달라지는 부분을 찾아내고, 달라지지 않는 부분과 분리한다.  
💊 달라지는 부분을 찾아서 나머지 코드에 영향을 주지 않도록 **캡슐화**해야 한다.

#### 캡슐화의 장점
바뀌는 부분만 따로 뽑아서 캡슐화하면 나중에 바뀌지 않는 부분에는 영향을 미치지 않고, 그 부분만 고치거나 확장할 수 있다.  
코드를 변경하는 과정에서 의도치 않게 발생하는 일을 줄이면서 시스템의 유연성을 확장시킬 수 있다.

##### 바뀌는 부분과 바뀌지 않는 부분 골라내기
**바뀌지 않는 부분**  

```java
public class Duck {
  public void swim() { }

  public abstract void display(); 
}
```

**바뀌는 부분**  
- 꽥꽥 거리는 행동 `quack()`
- 나는 행동 `fly()`  
Duck 클래스와는 완전히 별개로 2개의 클래스 집합을 만들어야 한다. 각 클래스 집합에는 각각의 행동을 구현한 것을 전부 집어넣는다. 

### 📘 디자인 원칙 2
> 구현보다는 인터페이스에 맞춰서 프로그래밍한다.  

💊  적용 방법
- 각 행동은 인터페이스로 표현하고, 인터페이스를 사용해서 행동을 구현한다.
- 특정 행동만을 목적으로 하는 클래스의 집합을 만든다. 
- 행동`behavior`인터페이스는 행동 클래스에서 구현한다. 
 
💊 기대 효과
- Duck 클래스에서 행동을 구체적으로 구현할 필요가 없다.
- 이전의 방식은 특정 구현에 의존했기 때문에 행동을 변경할 여지가 없었는데, 변경 후에는 행동을 구현한 클래스에 Duck 서브 클래스가 국한되지 않는다.

```java
public interface FlyBehavior {
  public void fly();
}
```

```java
public class FlyWithWings implements FlyBehavior {
  @Override
  public void fly() {
    // 나는 방법 구현
  }
}
```

```java
public class FlyNoWay implements FlyBehavior {
  @Override
  public void fly() {
    // 날 수 없음. 아무것도 하지 않음.
  }
}
```

###### reference

> HeadFirst Design Pattern  
> [추상 클래스](http://www.tcpschool.com/java/java_polymorphism_abstract)
