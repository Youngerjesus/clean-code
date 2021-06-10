> __클린 코드 Chapter 5. 형식 맞추기에 대해 정리합니다.__
>
> __학습할 내용은 다음과 같습니다.__
> - Principle 1. 적절한 행 길이를 유지하라 
> - Principle 2. 신문 기사처럼 작성하라
> - Principle 3. 개념은 빈 행으로 분리하라
> - Principle 4. 세로 밀집도
> - Principle 5. 수직 거리
> - Principle 6. 가로 형식 맞추기
> - Principle 7. 가로 공백과 밀집도
> - Principle 8. 가로 정렬
> - Principle 9. 들여쓰기
> - Principle 10. 팀 규칙
>
> __Reference__
>
> - [Clean Code 클린 코드 애자일 소프트웨어 장인 정신](http://www.yes24.com/Product/Goods/11681152)
***

## Intro

프로그래머라면 형식을 깔끔하게 맞춰서 코드를 짜야한다. 그래서 코드를 읽는 사람이 느꼈을 때 코드가 깔끔하고 일관적이며 꼼꼼하다고 느끼도록 해야한다.

단순히 돌아가는 코드를 짜지 말자. 

형식을 맞춰서 코드를 짜면 가독성이 좋아진다. 이런 가독성 수준은 유지보수 용이성과 확장성에 계속 영향을 미친다. 

그러므로 형식 없이 맹목적으로 짜지 말자.

***

## Principle 1. 적절한 행 길이를 유지하라 

소스 코드의 길이는 얼마가 적당할까? 

자바에서 크고 작은 프로젝트 7개를 분석ㄹ한 결과 대평균 파일 크기는 약 65줄에 전체 파일의 1/3이 40줄에서 100줄 정도이다. 

가장 긴 파일은 400줄 정도 된다. 

대부분 파일은 500줄을 넘어가는 파일이 없고 200줄 미만이다. 

그러므로 200줄 미만으로도 거대한 시스템을 만들 수 있다. 

이렇게 파일을 작게 만들라는 이유는 작은 파일이 큰 파일보다 이해하기 쉽다. 

***

## Principle 2. 신문 기사처럼 작성하라

아주 좋은 신문 기사를 떠올려보자. 어떻게 구성이되는가? 

최상단에 기사를 요약하는 표제가 나온다. 독자는 표제를 보고서 기사를 읽을지 말지 결정한다. 

첫 문단은 전체 기사 내용을 요약한다. 그리고 세세한 사실은 숨기고 큰 그림을 먼저 보여준다.

쭉 읽으면서 세세한 내용이 드러난다. 

소스 파일도 신문 기사와 비슷하게 작성한다. 

이름은 간단하면서 설명이 가능하게 짓고 이름을 보고 올바른 모듈을 보고 있는지 판단하도록 만든다. 

그리고 소스 파일의 첫 부분은 고차원 개념과 알고리즘을 설명하고 아래로 내려갈수록 의도를 세세하게 묘사한다. 

***

## Principle 3. 개념은 빈 행으로 분리하라

거의 모든 코드는 왼쪽에서 오른쪽으로 위에서 아래로 읽혀진다. 

각 행은 수식이나 절을 나타내고 행 묶음은 완결된 생각을 나타낸다고 생각하자.

그리고 생각과 생각 사이에는 빈 행을 넣어서 분리하자. 

이렇게 해야 코드 가독성이 올라간다. 다음과 같은 예제를 보자.

```java
package com.example.springrestapidemo;

import org.springframework.beans.factory.annotation.Autowired;
```

패키지와 import 문 사이에 빈 행이 들어간다. 개념과 개념 사이에는 빈 행을 넣어서 구별하면 좋다.

다음 예를 보자

```java
@RestController
public class SampleController {

    @GetMapping("/hello")
    public String hello() throws InterruptedException {
        Thread.sleep(5000);
        return "hello";
    }
    @GetMapping("/greeting")
    public String greeting() throws InterruptedException {
        Thread.sleep(5000);
        return "greeting";
    }
    @GetMapping("/hallo")
    public String hallo() throws InterruptedException {
        Thread.sleep(5000);
        return "hallo";
    }
}

vs

@RestController
public class SampleController {

    @GetMapping("/hello")
    public String hello() throws InterruptedException {
        Thread.sleep(5000);
        return "hello";
    }
    
    @GetMapping("/greeting")
    public String greeting() throws InterruptedException {
        Thread.sleep(5000);
        return "greeting";
    }
    
    @GetMapping("/hallo")
    public String hallo() throws InterruptedException {
        Thread.sleep(5000);
        return "hallo";
    }
}
```
둘 중에 뭐가 더 가독성이 나은가? 빈 행은 너무나도 간단한 규칙이지만 영향력은 크다. 

***

## Principle 4. 세로 밀집도

줄바꿈이 개념을 분리한다면 세로 밀집도는 연관성을 잇는다.

같은 연관성을 가진 개념이라면 서로 붙여놓아야 한다.

다음 두 예를 비교해보자.

```java

public class ReportConfig {
    /*
     * 리포터 리스너의 클래스 이름 
     */
    private String m_className;
    
    /*
     * 리포터 리스너의 속성
     */
    private List<Property> m_properties = new ArrayList<>();
    public void addProperty(Property property){
        m_properties.add(property); 
    }
}

vs

public class ReportConfig {
    
    private String m_className;
    private List<Property> m_properties = new ArrayList<>();
    
    public void addProperty(Property property){
        m_properties.add(property);
    }
}

```
***

## Principle 5. 수직 거리

함수의 연관 관계나 동작 방식을 이해하기 위해서 코드를 왔다갔다 한 경험이 있는가? 

이런 경험은 시간과 노력을 소비한다. 

서로 밀접한 개념은 세로로 가까이 둬야한다. 세로 거리로 밀접한 개념을 표현한다. 

### 변수 선언 

변수는 사용하는 위치에 최대한 가까이 둔다. 예는 다음과 같다. 

```java
private static void readPreferences(){
        InputStream is = null;
        try {
            is = new FileInputStream(getPreferencesFile());
            setPreference(new Property(getPreferences()));
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

```java
public int countTestCases(){
        int count = 0;
        for(Test each : tests){
            count += each.countTestCases(); 
        }
    }
}
```

### 인스턴스 변수

인스턴스 변수는 클래스 맨 처음에 선언한다. 

변수 간에 세로로 거리를 두지 않는다. 잘 설계한 클래스는 많은 클래스 메소드가 인스턴스 변수를 사용하기 떄문이다.

***

### 종속 함수

한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치하면 좋다. 

더 좋게 만들려면 호출하는 함수를 호출되는 함수보다 먼저 두면 좋다. 

***

### 개념적 유사성 

개념적인 친화도가 높은 코드는 서로 붙어 있도록 작성한다. 

이 말은 비슷한 동작을 수행하는 코드는 서로 붙여놓으면 좋다는 뜻이다. 예시를 보자.

```java

static void assertTrue(boolean condition){
	...
}

static void assertFalse(boolean condition){
	...
}
```

***

## Principle 6. 가로 형식 맞추기

한 행은 가로로 얼마나 길어야 할까?

아까처럼 자바 프로젝트 7개를 분석해보자. 결과는 놀랍게도 규칙적이다.

20자 ~ 60자 사이의 행이 총 행의 40%에 도달한다. 10자 미만은 30% 정도로 보인다.

80자 이후부터는 급격하게 감소한다. 

100자나 120자에 도달해도 나쁘지 않다. 하지만 그 이후부터는 좋지 않다. 

***

## Principle 7. 가로 공백과 밀집도

가로 공백을 이용해서도 개념을 강조할 수 있다. 

다음 예를 보자.

```java
private void measureLine(String line){
	lineCount++;
    int lineSize = line.length();
    totalChars += lineSize;
    lineWidthHistogram.addLine(lineSize, lineCount); 
}
```

할당 연산자를 강조하기 위해 양 옆으로 공백을 줬다.

인수 사이에 공백을 줘서 인수들을 별개로 보이도록 했다. 

***

## Principle 8. 가로 정렬

다음과 같이 가로 정렬을 하지말자.

```java
public FitNessExpeditor(Socket s, FitNesseContext context){
    this.context = context;
    socket =       s;
    input =        s.getInputStream(); 
    output =       s.getOutputStream(); 
}
```

이렇게 가로정렬을 하면 이상한 부분을 강조하게 된다. 변수 유형을 보는게 아니라 오른쪽 피연산자에 집중하게 된다. 

***

## Principle 9. 들여쓰기 

소스 파일은 윤곽도와 비슷하다. 

범위로 이뤄진 계층을 표현하기 위해 우리는 들여쓴다. 

들여쓰는 정도는 계층에서 코드가 자리잡은 수준에 비례한다. 클래스 정의처럼 파일 수준의 문장은 들여쓰지 않는다. 

때로 간단한 if 문이나 while 문 짧은 함수에서 들여쓰기 없이 표현할려는 마음이 생기지만 들여쓰기 하는게 낫다. 


***

## Principle 10. 팀 규칙

프로그래머라면 각자 선호하는 규칙이 있다. 

하지만 팀에 속한다면 팀 규칙을 따라야 한다. 그래야 소프트웨어가 일관성있게 보일 수 있다. 

***










