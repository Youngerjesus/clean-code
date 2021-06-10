> __클린 코드 Chapter 3. 함수에 대해 정리합니다.__
>
> __학습할 내용은 다음과 같습니다.__
> - Principle 1. 가능한 함수를 작게 만들어라
> - Principle 2. 함수는 한 가지만 해라
> - Principle 3. 함수 당 추상화 수준은 하나로
> - Principle 4. Switch 문
> - Principle 5. 서술적인 이름을 사용해라
> - Principle 6. 함수 인수
> - Principle 7. 부수 효과를 일으키지 마라
> - Principle 8. 명령과 조회를 분리해라
> - Principle 9. 오류 코드보다 예외를 사용하라
> - Principle 10. 반복하지 마라 
>
> __Reference__
>
> - [Clean Code 클린 코드 애자일 소프트웨어 장인 정신](http://www.yes24.com/Product/Goods/11681152)
***

## Intro 

어떤 프로그램이든 가장 기본적인 단위는 함수이다. 

이 장에서 다룰 내용은 다음과 같다.

- 읽기 쉽고 이해하기 쉬운 함수는 무엇인가? 

- 의도를 분명하게 하는 함수를 작성하려면 어떻게 해야하는가?

- 함수에 어떤 속성을 부여해야 함수 내부를 직관적으로 이해할 수 있을까? 

***

## Principle 1. 가능한 함수를 작게 만들어라 

함수를 작게 만든다는 것은 얼마를 강조해도 지나치지 않다.

이 책에서 왜 함수를 작게 만들라는 뜻일까? 

내 생각엔 함수의 라인이 길어질수록 함수를 읽는 사람이 생각하는 경우의 수가 많아 지기 떄문인 것 같다. 

함수가 짧다면 생각하는 경우의 수도 얼마되지 않기 때문에 간단히 함수의 의도를 이해할 수 있다.

그렇다면 함수는 어느정도로 짧아야 할까? 

켄트 백이 지은 자바 스윙 프로그램은 대부분 함수는 2 ~ 4줄 이내였다고 한다. 

권장하는 함수 길이는 다음과 같다.

```java
public static String renderPageWithSetupsAndTeardowns(PageData pagaData, boolean isSuite) throws Exception{
	if(isTestPage(pageData){
    	includeSetupAndTearDownPages(pageData, isSuite);
    }
    return pageData.getHtml(); 
}
```

그리고 함수 if 문 / else 문 / while 문에 들어가는 블록은 한 줄이어야 한다. 중첩 구조가 생길만큼 함수가 커져서는 안된다.


***

## Principle 2. 함수는 한 가지만 해라

함수는 한 가지를 해야한다. 그리고 그 한 가지를 잘해야한다. 

이 한 가지라는 말이 모호하게 들릴 수 있다. 함수를 한 줄로 끝내지 않는 이상 여러가지 일을 할 수 밖에 없다고 생각할 수 있다.

여기서 말하는 한 가지는 함수 이름 아래서 추상화 수준이 하나인 일을 말한다.

예를 들어서 설명하자면 함수 Function의 추상화 수준이 A라고 하자.

그리고 그 A를 수행하기 위해 해야하는 다음 단계의 추상화 일은 {b,c,d} 라고 하자. 

그러면 함수 Funciton은 {b,c,d}를 하는 세 가지 일을 하는게 아니라 그것들을 추상화 한 A라는 한 가지 일을 하는 것이다. 

그리고 여기서 또 중요한 건 이후에도 설명하겠지만 {b,c,d}를 포함하는 광범위한 추상화 수준을 잡는게 아니라 최소한의 A라는 추상화 수준을 잡는게 중요하다. 

그렇지 않으면 이름이 뒤바뀔 수 있다. 

그리고 {b,c,d}의 추상화 수준은 모두 동일해야한다. 그래야 읽는 사람이 예측하면서 읽기 쉽다.

***

## Principle 3. 함수 당 추상화 수준은 하나로

함수가 확실한 한 가지 작업을 수행하려면 함수의 추상화 수준은 모두 동일해야한다. 

함수 내에서 구체적인 디테일한 사항과 개념적인 사항들 즉 추상화 수준이 높은 사항들이 섞이면 읽는 사람은 헷갈린다. 

코드는 위에서 아래로 내려가면서 읽으며 추상화 수준이 모두 동일해야 자연스럽게 읽어진다.


***
## Principle 4. Switch 문

Switch 문은 작게 만들기 어렵다. 기본적으로 함수가 크게 될 수 있고 Switch 문을 포함한 함수를 한 가지 일로 추상화 하는게 어려울 수 있다.

그렇지만 Switch 문을 완전히 피하긴 어렵다.

다음 소개할 예는 다형성을 이용해 Switch 문을 처리하는 예제를 보겠다. 

먼저 N가지 일을 하는 Switch 문을 보자. 

```java
public Money calculatePay(Employee e) throws InvalidEmployeeType{
	switch(e.type){
    	case COMMISIONED: 
        	return calculateComissionedPay(e); 
        
        case HOURLY:
        	return calculateHourlyPay(e);
            
        case SALARIED:
        	return calculateSalariedPay(e);
            
        default:
            throw new InvalidEmployeeType(e.type);
    
    }
}
```


이 함수는 직원 유형에 따라 다른 값을 계산해 반환하는 함수다. 즉 SRP(Single Responsibility Principle)을 따른다. 

이 함수의 문제는 여럿 있다. 직원 유형이 추가되면 이 함수에도 추가해야하며 직원이 수정되면 이 함수도 수정해야 한다. OCP(Open Closed Principle)을 위반한다. 

그리고 가장 심각한 문제는 이 함수와 유사한 Switch 문을 가진 함수가 여러개 존재할 수 있다는 점이다. 

하지만 이 함수가 다형성을 이용해 다음과 같이 되면 어떨까? 

```java
public Money calculatePay(Employee e) {
	return e.calculatePay(); 
}
```

아까와 다르게 직원 유형이 추가되고 수정된다 해도 변경할 사항이 없다.

이런 구조를 이용하기 위해서는 DI(Dependency Injection)이나 처음 딱 한번 직원 유형에따라 직원을 생성해주는 클래스가 필요하다.

```java
public abstract class Employee{
	public Money calculatePay(); 
}

public interface EmployeeFactory(){
	public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType
}

public class EmployeeFactoryImpl implements EmployeeFactory{

    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType{
    	switch(e.type){
    	case COMMISIONED: 
        	return new CommisionedEmpolyee();  
        
        case HOURLY:
        	return new HourlyEmployee();
            
        case SALARIED:
        	return new SalariedEmployee();
            
        default:
            throw new InvalidEmployeeType(e.type);
    
    }
    }
}
```

Switch 문을 피할 수 있는 방법은 없지만 딱 한번 Switch 문은 참아줄 수 있다. 

***

## Principle 5. 서술적인 이름을 사용해라 

프로그램 내에서 좋은 이름은 얼마를 강조해도 지나치지 않다. 

만약 함수의 이름을 읽고 내가 예측한 동작과 함수의 동작이 같다면 얼마나 읽기 쉽겠는가 그걸 위해서 서술적인 이름을 사용하는게 좋다.  

이름이 길어도 괜찮다. 길고 서술적인 이름이 짧고 어려운 이름보다 낫다. 

서술적인 이름 예시는 다음과 같다.

- SeupTeardownIncluder()

- render()

- isTestable()

- includeSetupAndTeardownPages()

이름을 짓느라 시간이 들어도 괜찮다. 요즘 IDE는 쉽게 모든 이름을 바꿀  수 있다. 

그리고 이름은 유사한 개념을 가졌다면 통일되게 짓는게 좋다. 

예를 들면 다음과 같이 말이다.

- includeSetupAndTeardownPages()

- includeSetupPages()

- includeSuiteSetupPage() 

이렇게 지으면 한 가지의 함수만 파악하면 다른 함수는 무슨 일을 하는지 쉽게 예상할 수 있다.

***

## Principle 6. 함수 인수 

함수에서 이상적인 인수 개수는 0개이다. 

인수가 늘어갈수록 함수를 읽는 사람은 인수까지 생각해서 함수의 동작을 예측해야하기 떄문에 더 어렵다. 

그러므로 3개 이상의 인수를 갖는 함수는 피하는게 좋다. 

최선은 입력 인수가 없는 경우이며 차선은 입력 인수가 1개인 경우다.

includeSetupPage()보다 includeSetupPageInto(new PageContent)가 더 이해하기 어렵다. 

### 많이 쓰는 단항 형식

함수를 단항 형식으로 쓰는 경우는 크게 두 가지가 있다. 

하나는 인수에 질의를 던지는 경우 (e.g boolean isFileExist("MyFile"))이 있고

다른 하나는 인수로 뭔가를 변환해 결과를 반환하는 경우다. (e.g InputStream fileOpen("MyFile))

fileOpen() 함수는 String 형식의 파일 이름을 InputStream으로 반환받는 함수이다. 

다소 드물게 사용하지만 출력 형식이 없는 단항 함수도 사용하는 경우가 있다. 

얘를 들면 이벤트 함수다. 이런 함수는 출력 인수가 없고 시스템의 상태를 바꿀 때 사용한다. (e.g passwordAttemptFailedName(int attempts)가 좋은 예이다. 


### 플래그 인수

함수에 플래그 인수를 사용하지 말자. 

이 말은 함수가 여러개의 일을 처리한다는 뜻이니 기존의 법칙들과는 위반된다.


### 이항 함수

인수가 2개인 함수는 인수가 1개인 함수보다 이해하기 어렵다. 

writeField(name) 보다 writeField(outputStream, name)이 더 이해하기 어렵다. 

하지만 때로는 이항 함수가 더 적합한 경우가 있다. 

인수가 두개로 예측할 수 있는 것들로 예를 들면 new Pointer(x, y)가 있다. 직교 좌표계는 항상 두 값을 취하므로 이런 경우는 이항 함수가 더 적합하다.

그리고 assertEquals(expected, actual)과 같은 당연하게 수행되는 함수들도 있다. 


### 삼항 함수

안수가 3개인 함수는 인수가 2개인 함수보다 훨씬 더 이해하기 어렵다. 

그러므로 가능한 사용하지 말자. 

### 인수 객체

인수가 2 - 3개 필요하다면 일부를 독자적인 클래스 변수로 만들어서 사용할 수 있다.

예를 들면 다음과 같이 사용할 수 있다. 

```java
Circle makeCircle(double x, double y, double radius)

to

Circle makeCircle(Point center, double radius)
```

눈속임이라고 생각할 수 있지만 결국 추상화 한 개념을 표현한 것이다. 


### 인수 목록 

때로는 가변적인 인수 함수가 있다. 예를 들면 String.format()과 같은 함수이다.

하지만 이런 가변적인 함수도 인수가 모두 동일하다면 인수를 줄이는 List 형식으로 받아올 수 있다. 


### 동사와 키워드 

함수의 의도에다 인수의 순서까지 제대로 표현하기 위해선 좋은 이름이 당연하게 필요하다. 

이런 이름을 짓기 위해서 단항 함수라면 동사 / 명사 위주로 표현하면 좋다. writeField(name)과 같이.

아니면 함수의 키워드를 추가해 인수를 예측할 수 있도록 하면 좋다. 

assertExpectedEqualsActual(expected, actual) 처럼 작성하면 좋다.

***

## Principle 7. 부수 효과를 일으키지 마라
 
함수에서 한 가지 일만 하기로 했는데 부수 효과를 일으키는 다른 일은 하면 안된다. 
 
만약 꼭 필요하게 부수 효과를 일으켜야 한다면 이름에 어떤 부수 효과를 일으키는지 명시하는게 좋다. 
 
### 출력 인수 
 
함수의 인수를 입력으로 이용하되 출력으로까지 이용하면 어색해진다.
 
예를 들면 appendFooter(str)과 같은 함수가 있다고 생각해보자.
 
이 함수는 str 뒤에 무언가의 string을 붙이는 걸까 무언가에 str을 붙이는 걸까? 출력 인수는 str 이라는걸 이해하지만 이 함수의 동작을 정확하게 이해하기 힘들다. 

그러므로 이 함수의 정확한 동작을 보기 위해서는 함수 선언부를 직접 찾아봐야 하는 노력이든다.

함수에서 인수는 입력으로 사용하되 인수를 변경해야 하는 일이 있다면 this를 이용한 객체에게 맡기자

report.appendFooter() 이런식으로. 

 ***
 
 ## Principle 8. 명령과 조회를 분리해라 
 
 함수가 한 가지일만 하라는 내용을 보완해주는 원칙같다. 
 
 함수는 뭔가를 수행하거나 뭔가를 답을 주거나 둘 중 하나만 해야한다. 
 
 만약 함수가 이 둘 모두를 한다면 이해하기 어렵다.
 
 예를 들면 다음과 같은 함수가 있다고 생각해보자.
 
 public boolean set(String attribute, String value); 
 
 이 함수는 attribute에 해당하는 속성 값을 찾은 후 value로 수정하고 성공하면 true를 리턴하는 함수다. 
 
 함수를 읽는 사람은 혼란을 겪을 수 있으므로 이런식으로 코드를 짜면 안된다.
 
 
 ***
 
 ## Principle 9. 오류 코드보다 예외를 사용하라
 
 명렴 함수에서 오류 코드를 반환하면 오류 코드를 곧바로 처리해야하는 문제에 직면하게 된다.
 
 이 경우 오류 코드를 처리하는 문을 중첩적으로 사용할 수 있고 코드가 지저분해진다. 
 
 ```java
 if(deletePage(page) == E_OK){
    if(configKey.deleteKey(key) == E_OK){
    	...
    }else{
    	logger.log("delete Key Failed"); 
    }
 }else {
  	logger.log("delete Page failed");
 }
 ```
 
 이런 오류 코드를 사용하는것보다 예외를 사용하면 예외 처리를 뺼 수 있으므로 코드가 더 깔끔해진다.
 
 ```java
 try{
 	deletePage(page);
    registry.deleteReference(page.name));
    configKeys.deleteKey(page.name.makeKey()); 
 }catch(Exception e){
 	logger.log(e.getMessage()); 
 }
 ```
 
 #### try-catch 블록 뽑아내기 
 
 try-catch 블록은 정상 동작와 예외 동작이 뒤섞일 수 있으므로 혼란을 줄 수 있고 오류 처리도 한 가지 작업으로 봐야한다. 
 
 그러므로 오류를 처리하는 함수는 오류만 처리하도록 구성해야하므로 try - catch 블록은 뽑아내는 방식이 적합하다.
 
 ```java 
 public void delete(Page page){
    try{
    	deletePageAndAllReference(page); 	
    }catch(Exception e){
    	logError(e); 
    }
 }
 
 public void deletePageAndAllReference(Page page) throw Exception{
 	deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.makeKey()); 
 }
 ```
 
 ### 의존석 자석(Dependency Magnet)
 
오류 코드에대해 좀 더 이야기 하자면 오류 코드를 사용한다는 이야기는 클래스든 열거형 변수든 어디선가 오류 코드를 정의하고 import 해서 사용하고 있다는 의미이다.

이 말은 오류 코드가 변경된다면 import 하고 있는 모든 클래스에서도 변경을 해야함을 말한다.

이런 관계가 의존성 자석이다. 매우 번거롭기 때문에 Exception을 사용하느게 좋다.


***

## Principle 10. 반복하지 마라

소프트웨어에서 중복은 악이다. 

반복되는 알고리즘이 여러 함수에서 사용된다고 생각해보자. 

이 알고리즘을 고치려면 여러 함수를 손봐야한다. 즉 한 곳이라도 빠뜨리면 문제가 생길 수 있다. 

이런 중복되는 부분은 따로 빼서 가독성을 높이고 오류가 발생할 확률을 줄이자. 

## 결론 

소프트웨어를 짜는 행위는 글짓기와 유사하다. 한번에 완성시켜서 만들려고 하지말고 다듬으면서 완성하도록 하자. 

이렇게 다듬는 중에서도 테스트 코드를 통과하는지 매번 검사하자. 

 
 
 