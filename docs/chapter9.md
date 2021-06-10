> __클린 코드 Chapter 9. 단위 테스트에 대해 정리합니다.__
>
> __학습할 내용은 다음과 같습니다.__
> - Principle 1. 깨끗한 테스트 코드 유지하기 
> - Principle 2. 테스트 당 assert 하나
>
> __Reference__
>
> - [Clean Code 클린 코드 애자일 소프트웨어 장인 정신](http://www.yes24.com/Product/Goods/11681152)

***

## Intro 

옛날에는 급조로 테스트 코드를 작성해서 테스트하고 버렸다면 지금은 꼬치꼬치 따지며 테스트 코드를 작성한다.

TDD(테스트 주도 개발)은 실제 코드를 짜기 전에 단위 테스트부터 짜라고 요구한다.

다음 TDD 세 가지 법칙을 살펴보자

- 첫째. 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.

- 둘째. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.

- 셋째. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

위 법칙을 따르면 실제 코드가 나오기전에 테스크 코드가 먼저 나온다. 이렇게 일하면 실제 코드를 사실상 전부 테스트하는 테스트 케이스가 나온다.

하지만 실제 코드와 맞먹을 정도로 방대한 테스트 코드는 심각한 관리 문제를 유발하기도 한다.

***

## Principle 1. 깨끗한 테스트 코드 유지하기 

어떤 사람은 테스트 코드에 실제 코드와 동일한 품질 기준을 적용하짐 말아야 한다고 말하기도 한다. 

하지만 테스트 코드를 깨끗하게 유지하는 건 중요하다.

실제 코드가 진화하고 바뀔때마다 테스트 코드도 변경된다. 

이런 상황에서 테스트 코드가 지저분하다면 실제 코드를 수정하는 시간보다 테스트 코드를 수정하는 시간이 더 길어진다. 

규모가 커질수록 테스트 코드를 유지하고 보수하는 비용도 늘어난다. 

그렇다고 테스트 코드를 안쓰면 프로그램의 결함도가 높아질 수 밖에 없다. 

그러면 프로그래머는 코드를 변경하면 득보다 해가 더 많다고 생각해 코드를 변경하지 않는다. 그러면서 코드는 ㅁ아가진다. 

테스트 코드는 실제 코드 못지 않게 중요하다. 

테스트 코드를 깨끗하게 유지하지 않으면 결국은 잃어벌니다.

테스트 케이스가 없으면 실제 코드를 유연하게 만드는 버팀목도 사라진다. 

코드에 유연성, 유지보수성, 재사용성을 제공하는 버팀목이 바로 단위 테스트다. 

테스트 케이스가 있다면 변경이 두렵지 않다. 

그렇다면 __깨끗한 테스트 코드를 만들려면 어떻게 해야할까__?

첫째도 가독성 둘째도 가독성 셋째도 가독성이다. 

실제 코드보다 테스트 코드에 더욱 가독성이 중요하다. 

가독성을 위해서라면 다음과 같이 BUILD-OPERATE-CHECK 패턴을 사용하자. 

이 패턴은 테스트를 명확히 세 부분으로 나눈다. 첫 부분은 테스트 자료를 만든다. 두 번째 부분은 테스트 자료를 수행한다.  세 번째 부분은 수행한 결과가 올바른지 확인한다.

다음 예를 통해서 한번 보자

```java
public void testGetPageHierarchyAsXml() throws Exception{
        makePages("PageOne", "PageTwo", "PageThree");
        
        submitRequet("Root", "Type:Pages");
        
        assertResponseContains("<name> PageOne </name>", "<name> PageTwo </name>","<name> ChildOne </name>"); 
}
```

그리고 __테스트 코드에 적용하는 표준은 실제 코드 표준과는 확실히 다르다.__

테스트 코드는 간결하고 표현력이 풍부해야하지만 실제 코드만큼 효율적인 필요는 없다.

실제 환경이 아니라 테스트 환경에서 돌아가는 코드이기 때문이다. 

다음과 같은 두 예제를 비교해보자.

```java
public void turnOnCoolerAndBlowerIfTooHot() throws Exception{
    hw.setTemp(WAY_TOO_HOT); 
    contoller.tic();
    assertTrue(hw.heaterState()); 
    assertTrue(hw.blowerState()); 
    assertTrue(hw.coolerState()); 
    assertTrue(hw.hiTempAlarm()); 
    assertTrue(hw.loTempAlarm()); 
}


vs

public void turnOnCoolerAndBlowerIfTooHot(){
        tooHot();
        
        assertEquals("hbchl", hw.getState());
}

public String getState(){
 	String state = "";
    state += heater ? "H" : "h"; 
    state += blower ? "B" : "b"; 
    state += cooler ? "C" : "c"; 
    state += hiTempAlarm ? "H" : "h"; 
    state += loTempAlarm ? "L" : "l"; 
}
```

위에 있는 코드는 모든 assert 문을 비교해서 봐야하고 tic() 메소드가 무슨일을 하는지도 모른다. 

아래에 있는 코드는 tooHot() 이라는 메소드를 만들어서 가독성을 올렸다. 

그리고 여러번의 assert 문 대신에 추상적인 표현을 써서 한번에 표현했다.

실제 코드의 기준이라면 그릇된 정보를 표현하지 마라 라는 기준을 어겼지만 테스트 코드와 실제 코드의 요구사항은 다르다.

그리고 테스트 코드의 getState() 메소드의 경우에 StringBuffer 대신 String을 사용했다.

실제 성능을 중시한다면 StringBuffer를 사용해야 한다. 


***

## Principle 2. 테스트 당 assert 하나

JUnit으로 테스트 코드를 짤 때는 함수마다 assert문 하나만 사용해야한다. 

테스트 코드 하나당 한 개념만 테스트 해야한다고 말하는게 정확할지도 모른다. 

그리고 테스트 코드는 BUILD-OPERATE-CHECK 패턴에서 given-when-then 관례를 사용하면 가독성을 높일 수 있다. 

다음 예제를 보자.

```java
public void testGetPageHierarchyAsXml() throws Exception{
        givenPages("PageOne", "PageTwo", "PageThree");
        
        whenSubmitRequet("Root", "Type:Pages");
        
        thenAssertResponseContains("<name> PageOne </name>", "<name> PageTwo </name>","<name> ChildOne </name>"); 
}
```

그리고 이런 테스트 코드당 한 assert문만 사용한다는 규칙을 따르면 중복이 되는 코드가 많아질 수 있다.

이 경우 디자인 패턴 중 하나인 TEMPLATE METHOD 패턴을 사용하면 중복을 줄일 수 있다.

given-when 부분을 부모 클래스에 두고 then 부분을 자식 클래스에 두도록 하면 된다. 

아니면 @Before 함수에 given / when 부분을 두고 @Test 함수에 then 부분을 넣어도 된다. 


### F.I.R.S.T

깨끗한 테스트 코드는 다음 다섯 가지 규칙을 따른다. 

#### F (Fast)

테스트는 빨라야 한다. 테스트는 빨리 돌아야 한다. 느리면 자주 돌릴 엄두를 못낸다. 자주 돌리지 않으면 초반에 문제를 찾기 힘들다.

#### I (Independent)

테스트는 서로 의존하면 안된다.

한 테스트가 다음 테스트가 실행될 환경을 준비해서는 안된다.

각 테스트는 독립적이어야 한다. 

#### R (Repeatable)

테스트는 어떤 환경에서도 반복 가능해야한다. 

테스트를 어느 환경에서든 돌릴 환경이어야 한다. 

#### S (Self-Validating)

테스트는 부울 값으로 결과를 내야한다. 수작업으로 비교해서 안된다.

#### T (Timely)

테스트는 적시에 작성해야한다. 실제 코드보다 먼저 테스트 코드를 작성해야 실제 코드가 테스트하기 어렵다는 사실을 알게될 수 있다. 

***

## 결론

이 장은 주제를 수박 겉핥기 정도로만 훑었다.

사실상 테스트 코드 주제는 책 한 권을 할애해도 모자라다. 테스트 코드는 실제 코드만큼이나 중요하다.

테스트 코든느 실제 코드의 유연성, 유지보수성, 재사용성을 보존하고 강화하기 때문이다. 



