---
title: "[디자인 패턴] Singleton Pattern (싱글톤 패턴)"
date: "2019-10-10T02:15:00.000Z"
layout: post
draft: false
path: "/posts/singleton-pattern"
category: "design pattern"
tags:
  - "Design Pattern"
  - "java"
description: "Singleton Pattern (싱글톤 패턴)"
---

### Singleton Pattern 이란?

싱글톤 패턴이란 인스턴스를 하나만 만들어 사용하기 위한 패턴이다. 커넥션 풀, 스레드 풀, 디바이스 설정 객체 등의 경우, 인스턴스를 여러 개 만들게 되면 자원을 낭비하게 되거나 버그를 발생시킬 수 있으므로 오직 하나만 생성하고 그 인스턴스를 사용하도록 하는 것이 이 패턴의 목적이다.

### Singleton Pattern을 쓰는 이유

- 고정된 메모리 영역을 얻으면서 한번의 `new` 로 인스턴 스를 사용하기 때문에 메모리 낭비를 방지
- 싱글톤으로 만들어진 클래스의 인스턴스는 전역 인스턴스이기 때문에 다른 클래스의 인스턴스들이 데이터를 공유하기 쉽다.
- DBCP(Database Connection Pool)처럼 공통된 객체를 여러개 생성해서 사용하는 상황에서 사용
- 인스턴스가 절대적으로 한개만 존재하는 것을 보증하고 싶을 경우

### Singleton 클래스와 인스턴스를 만드는 방법

**version 1.0**

```java
public class Singleton {
  private static Singleton instance;
  
  private Singleton() {}
  
  public static Singleton getInstance() {
    if (instance == null) {
      instance = new Singleton();
    }
    return instance;
  }
}
```

이 코드는 위험하다. 멀티 스레딩 환경에서 싱글턴 패턴을 적용하다보면 문제가 발생할 수 있다. 동시에 접근하다가 하나만 생성되어야 하는 인스턴스가 두 개 생성될 수 있는 것이다.

**version 2.0**

```java
public class Singleton {
  private static Singleton instance;
  
  private Singleton() {}
  
  public static synchronized Singleton getInstance() {
    if (instance == null) {
      instance = new Singleton();
    }
    return instance;
  }
}
```

`synchronized` 키워드를 사용해서 thread-safe하게 만들었다. 하지만 `synchronized` 특성상 큰 성능저하가 발생한다.(실제로 약 100배 정도 느려진다고 한다.)

**version 3.0**

```java
public class Singleton {
  private static volatile Singleton instance;
  
  private Singleton() {}
  
  public static Singleton getInstance() {
    if (instance == null) {
      synchronized (Singleton.class) {
        if (instance == null ) {
          instance = new Singleton();
        }
      }
    }
    return instance;
  }
}
```

DCL(Double Checking Locking)을 써서 `getInstance()` 에서 동기화되는 영역을 줄일 수 있다. 초기에 객체를 생성하지 않으면서도 동기화하는 부분을 작게 만들었다. `thread-safe` 하면서도 처음 생성이후에 `synchronized` 블럭을 타지 않기 때문에 성능저하를 완하하였다. 그러나 완벽한 방법은 아니다. 이 코드는 멀티코어 환경에서 동작할 때, 하나의 CPU를 제외하고는 다른 CPU가 LOCK이 걸리게 된다. 그렇게 때문에 다른 방법이 필요하다.



**version 4.0**

```java
public class Singleton {
  private Singleton() {}
  
  private static class SingletonHolder {
    public static final Singleton instance = new Singleton();
  }
  
  public static Singleton getInstance() {
    return SingletonHolder.instance;
  }
}
```

이 방법은 중첩 클래스를 이용한 Holder를 사용하는 방법이다. `getInstance()` 메소드가 호출되기 전까지는 `Singleton` 인스턴스는 생성되지 않는다. 그리고 `getInstance()` 메소드가 호출되는 시점에 `SingletonHolder` 가 참조되고 그 때 `Singleton` 인스턴스가 생성된다.  *지연된 초기화(Lazy Initialization)* 기법을 사용하기 때문에 메모리 점유율 면에서 유리하고, `synchronized` 키워드를 사용하지 않았기 때문에 성는 문제도 보이지 않는다. 그러나 아직 인스턴스가 생성되지 않은 초기의 시점에서 두 스레드가 동시에 `getInstance` 를 실행하면 인스턴스가 두 개 생성될 것 같다. 하지만 최신 JVM은 클래스를 초기화하기 위한 필드 접근은 동기화한다. 초기화되고 나면 코드를 바꿔서 앞으로의 필드 접근에는 어떤 동가화나 검사도 필요하지 않게 된다. 그러므로 초기화 이후 다시 `getInstance()` 메소드가 호출된다고 하더라고 `new Singleton()` 은 호출되지 않는다.



참고

- https://jeong-pro.tistory.com/86
- https://asfirstalways.tistory.com/335

