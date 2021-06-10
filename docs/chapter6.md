> __클린 코드 Chapter 6. 객체와 자료구조에 대해 정리합니다.__
>
> __학습할 내용은 다음과 같습니다.__
> - Principle 1. 자료 추상화
> - Principle 2. 디미터 법칙
> 
> __Reference__
>
> - [Clean Code 클린 코드 애자일 소프트웨어 장인 정신](http://www.yes24.com/Product/Goods/11681152)
***

## Intro

변수를 비공개(private)으로 설정하는 이유가 있다. 

프로그래머가 변수에 의존하지 않도록 하기 위해서다. 하지만 변수의 set() 메소드와 get() 메소드를 제공한다면 비공개 변수를 쓴 의미가 있을까? 

그냥 그대로 어떤 변수인지 노출하니까 이렇게 쓰면 안된다. 아무 생각 없이 get() / set() 메소드를 추가하면 안된다.

***

## Principle 1. 자료 추상화

다음 예시를 보자.

```java
// 구체적인 Point 클래스  
public class Point {
    public double x;
    public double y; 
}

// 추상적인 Point 클래스 
public interface Point {
    double getX(); 
    double getY();
    void setCartesian(double x, double y); 
    
    double getR();
    double getTheta(); 
    void setPolar(double r, double theta); 
}
```

추상적인 Point 클래스는 직교좌표계를 쓰는지 극좌표계를 쓰는지 알 길이 없다. 

하지만 그럼에도 자료구조를 명백하게 표현해준다. 

추상 인터페이스를 제공해서 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야 진정한 클래스를 가질 수 있다. 

좌표를 읽을 때는 개별적으로 값을 읽지만 값을 설정할 땐 두 값을 동시에 설정하도록 강제한다. 

단순히 get()과 set()을 쓰지 않았다. 

다음 예를 보자. 

```java
// 구체적인 Vehicle 클래스 
public interface Vehicle {
    double getFuelTankCapacityInGallons(); 
    double getGallonsOfGasoline(); 
}

// 추상적인 Vehicle 클래스 
public interface Vehicle {
    double getPercentFuelRemain();
}
```

추상적인 Vehicle 클래스는 남은 연료 상태를 백분율이라는 추상적인 개념으로 알려준다. 그냥 단순히 자료를 세세하게 공개하지 않는다.

개념으로 표현해서 알려준다. 그러므로 변수에 의존하지 않도록 해준다.

#### 자료 / 객체 비대칭 

- __객체__ 는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개한다.
- __자료 구조__ 는 자료를 그대로 공개하며 별다른 함수는 제공하지 않는다. 

##### 자료 구조 지향 설계 
```java
//절차적인 도형 (Procedural Shape) - 자료 구조
public class Square {
  public Point topLeft;
  public double side;
}

public class Rectangle {
  public Point topLeft;
  public double height;
  public double width;
}

public class Circle {
  public Point center;
  public double radius;
}

public class Geometry {
  public final double PI = 3.141592653589793;

  public double area(Object shape) throws NoSuchShapeException {
    if (shape instanceof Square) {
      Square s = (Square)shape;
      return s.side * s.side;
    } else if (shape instanceof Rectangle) {
      Rectangle r = (Rectangle)shape;
      return r.height * r.width;
    } else if (shape instanceof Circle) {
      Circle c = (Circle)shape;
      return PI * c.radius * c.radius;
    }
    throw new NoSuchShapeException();
  }
}
```

`Square`, `Rectangle`, `Circle`  은 아무런 함수를 제공하지 않는 단순한 자료구조이고 도형의 동작은 `Geometry` 클래스에서 구현한다. 

`Gemometry` 클래스에서 함수를 추가하고자 해도 자료구조는 아무런 영향을 받지 않는다.

하지만 새로운 도형 자료 구조를 추가하고자 하면 도형의 동작을 책임지는 클래스들 즉 `Geometry` 라는 함수를 모두 고쳐야 한다. 그런 문제가 생긴다.

객체 지향을 보자. 

##### 객체 지향 설계

```java
//다형적인 도형 (Polymorphic Shape)
public class Square implements Shape {
  private Point topLeft;
  private double side;

  public double area() {
    return side * side;
  }
}

public class Rectangle implements Shape {
  private Point topLeft;
  private double height;
  private double width;

  public double area() {
    return height * width;
  }
}

public class Circle implements Shape {
  private Point center;
  private double radius;
  public final double PI = 3.141592653589793;

  public double area() {
    return PI * radius * radius;
  }
}
```

객체 지향의 설계에서는 새 도형을 추가해도 아무런 문제가 없다. 하지만 도형의 새 함수를 추가하고 싶다면 도형 클래스 모두를 고쳐야 한다. 

정리하면 이렇다.

##### 자료 구조 지향의 설계
- 기존의 자료 구조를 고치지 않으면서 새 함수를 추가하기 쉽다. 
- 하지만 새 자료 구조를 추가하려면 모든 함수를 고쳐야 한다. 

##### 객체 지향 설계
- 새 객체를 추가하기 쉽다. 
- 하지만 객체의 동작을 표현하는 함수를 추가하기는 어렵다. 함수를 추가하면 모든 객체의 함수를 고쳐야 한다. 

미래 지향적으로 보면 

***

## Principle 2. 디미터 법칙

디미터 법칙은 잘 알려진 휴리스틱으로 모듈은 자신이 조작하는 객체의 내부를 몰라야 한다는 법칙이다.

먼저 여기서 계속 말할 객체와 자료구조를 구별해서 설명하겠다.

객체는 조회 함수로 내부 구조를 공개하면 안된다. 하지만 자료구조는 당연히 내부 구조를 드러내야한다. 

그리고 자료구조가 빈이라면 표준 프레임워크의 정의를 따라 set() 메소드와 get() 메소드를 구현해야한다. 

이제 디미터 법칙에 대해 좀 더 정확하게 설명해보자면 디미터 법칙에서 클래스 C에 있는 메소드 f는 다음과 같은 메소드들만 호출해야 한다라고 주장한다.

- 클래스 C의 다른 메소드들

- 메소드 f가 생성한 객체의 메소드들

- 메소드 f의 인수로 들어온 객체의 메소드들

- 클래스 C의 인스턴스 메소드들

이 규칙에서는 메소드 f가 리턴하는 객체의 메소드를 호출하면 안된다. 

이 규칙을 간단히 정의하면 낯선 사람은 경계하고 친구랑만 놀라는 뜻이다. 

다음 코드는 객체라면 디미터 법칙을 어긴다.  

```java
final String output = ctxt.getOptions().getScratchDir().getAbsolutePath(); 
```

이와 같은 코드를 기차 충돌(train wreck)이라고 부른다. 여러 객체가 한 줄로 이어진 기차처럼 보이기 떄문이다. 

이렇게 쓰인 코드는 조잡하다. 그러므로 다음과 같이 디미터 규칙을 따르는 코드로 바꾸자.

```java
Options opts = ctxt.getOptions(); 
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath(); 
```

### 잡종 구조

이런 혼란으로 객체와 자료구조가 뒤섞인 잡종 구조가 나온다.

이런 잡종 구조는 새로운 함수는 물론이고 새로운 자료구조도 추가하기 어렵다. 그러므로 잡종 구조는 되도록 피하도록 하자. 

### 구조체 감추기 

객체 메소드 내에서 자신이 몰라야 할 객체를 알 필요는 없다. 즉 몰라도 문제 없는 객체를 굳이 속을 드러내도록 하지말자. 

그러므로 어떤 객체를 드러내라고 시키기 보단 어떤 행동을 하도록 만들자. 

***

## 결론

객체는 동작을 공개하고 자료를 숨긴다. 그래서 기존 동작을 변경하지 않으면서 새 객체 타입을 추가하기는 쉽다. 

반면 기존 객체에 새 동작을 추가하기는 어렵다. 자료 구조는 별다른 동작 없이 자료를 노출한다. 그래서 기존 자료구조에 새 동작을 추가하기는 쉬우나 

기존 함수에 새 자료 구조를 추가하기는 어렵다. 












