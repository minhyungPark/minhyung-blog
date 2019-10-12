---
title: "[JAVA] Static과 JVM 메모리"
date: "2019-10-10T17:20:00.000Z"
layout: post
draft: false
path: "/posts/java-static"
category: "java"
tags:
  - "java"
  - "jvm"
description: "Java의 static을 JVM 관점에서..."
---

## static 이란?

실제로 클래스가 로딩될 때 `static` 선언된 자원들은 JVM에서 메모리에 딱 한번만 올라간다.
먼저 자원들이 사용되려면 메모리를 할당받아야 한다.

Java에서 메모리를 할당받는 방법은 2가지가 있습니다.

1. 객체 생성
2. `static` 사용

static 의 장단점을 알아보기 전에 먼저 JVM 메모리 부분에 대해 간단히 알아보자.

## JVM(Java Virtual Machine)

개발자가 소스코드를 작성후에 실행을 시키면 컴파일러가 `.java` 파일을 `.class` Byte code 로 변환시킨다.

Class Loader는 `.class` 파일 ( + 라이브러리 ) 을 로드합니다. Class Loader는 로드한 class 파일을 메모리(Runtime Data Area) 에 올려준다. 

JVM의 메모리 구조 중, 메소드 영역(Method Area) 이 있다.

### Method Area

실행 중 어떠한 class가 사용되면, JVM은 해당 class의 class file을 읽어 분석한 후 class 관련 정보를 이 영역에 저장한다. 이때 class variable도 함께 저장된다.

객체 생성 후에 메소드를 실행하게 되면 해당 클래스 코드에 대한 정보를 method area에 저장하게 된다. 메소드 영역은 Type 정보를 가지고 있는 영역입니다.

우선 Class Variable만 알아보자.

**Class Variable**

- Class variable은 Static Variable이다.
- Static 변수는 Class Variable에 저장된다.
- 어디서든 공유해서 쓸 수 있다.
- Static 변수는 클래스가 사용되기 전에 미리 메모리에 올라간다.

## Static 의 장점

- Static 은 공용 자원이다.
- 제대로 사용한다면 메모리를 효율적으로 사용할 수 있다.
- 속도가 빠르다

## Static의 단점

- 무분별한 Static 사용은 오히려 메모리 낭비가 될 가능성이 있다.
  - static은 미리 메모리를 할당 받는다. 소멸되는 시점은 프로그램이 종료될 때이다. 따라서 사용을 안하고 있어도 메모리가 계속 잡혀있다. 그러나 객체 생성으로 만들어진 인스턴스는 GC에 의해 소멸이 될 수 있으므로 오히려 static의 사용이 메모리 낭비가 될 수 있다.

## 효율적인 사용법

- 공유하는 값에 사용한다.
- 절대적으로 변하지 않는 값에는 `final` 과 함께 사용한다.

## 정리

**static** 은 객체생성 없이 공용된 자원을 사용함으로써 메모리를 효율적으로 사용할 수 있지만 무분별한 사용은 프로그램에 악영항을 미칠 수 있다.


**참고**

- https://itpangpang.xyz/72?category=556738
- https://gmlwjd9405.github.io/2018/08/04/java-static.html

