> __클린 코드 Chapter 2. 의미 있는 이름에 대해 정리합니다.__
>
> __학습할 내용은 다음과 같습니다.__
>
> - Principle 1. 의도를 분명히 밝혀라
> - Principle 2. 그릇된 정보는 피해라 
> - Principle 3. 의미 있게 구분하라
> - Principle 4. 발음하기 쉬운 이름을 사용해라
> - Principle 5. 검색하기 쉬운 이름을 사용해라 
> - Principle 6. 인코딩을 피해라 
> - Principle 7. 자신의 기억력을 과시하지 마라 
> - Principle 8. 해법 영역에서 가져온 이름을 사용해라 
> - Principle 9. 문제 영역에서 가져온 이름을 사용해라 
> - Principle 10. 의미있는 맥락을 추가해라
> - Principle 11. 불필요한 맥락을 없애라
> 
> __Reference__
>
> - [Clean Code 클린 코드 애자일 소프트웨어 장인 정신](http://www.yes24.com/Product/Goods/11681152)
***

## Intro

소프트웨어에서 이름은 어디에서나 쓰인다.

변수에다가, 클래스와 패키지에다가 소스 파일에다가, 디렉토리에도 이름을 붙인다. 

이름을 잘 지으면 여러모로 편하다. 여기에서는 이름을 잘 짓는 여러가지 간단한 원칙을 소개하겠다.

## Principle 1. 의도를 분명히 밝혀라

의도가 분명한 이름은 코드를 읽는 사람에게 보다 많은 정보를 준다. 즉 코드 이해와 변경이 쉬워진다. 

다음과 같은 코드가 있다고 생각해보자.

```java
public List<int[]> getThem(){
	List<int[]> list1 = new ArrayList<int[]>(); 
    
    for(int[] x : theList){
    	if(x[0] == 4)
        	list1.add(x); 
    }
    
    return list1;
}
```

위 코드는 단순하다. 하지만 무슨 일을 하는지 짐작이 되는가?  

올바른 이름을 준 코드는 다음과 같다. 

```java
public List<int[]> getFlaggedCells(){
	List<int[]> flaggedCells = new ArrayList<int[]>(); 
    
    for(int[] cell : gameBoard){
    	if(cell[STATUS_VALUE] == FLAGGED)
        	flaggedCells.add(cell); 
    }
    
    return list1;
}
```

단순히 이름을 고쳤을 뿐이지만 함수가 하는 일을 이해하기는 더 쉬워졌다. 

***

## Principle 2. 그릇된 정보는 피해라 

프로그래머는 코드에 그릇된 단서를 남겨선 안된다. 

그릇된 의미는 코드를 읽는 사람에게 혼돈을 줄 수 있다. 

나름대로 널리 쓰이는 의미가 있는 단어를 다른 의미로 사용하면 안된다. 예를 들면 hp, aix, sco는 변수 이름으로는 적합하지 않다. 

여러 계정을 그룹으로 묶을 때 실제 List 자료구조가 아니라면 accountList라는 이름을 사용하지 않는다. 

그리고 서로 규칙없이 흡사한 이름을 쓰지 않는다. 

일관성있게 그리고 규칙있게 사용한 이름은 의미를 이해하는데 정보를 전달해주지만 그런 규칙이 없다면 혼돈을 줄 수 있다.

예를 들면 XYZXControllerForEfficientHandlingOfStrings 라는 이름을 사용하고 다른 모듈에서 XYZControllerForEfficientStorageOfStrings 라는 이름을 사용한다면 둘의 차이를 알기 힘들다. 

***


## Principle 3. 의미 있게 구분하라

이름을 지을땐 의미있는 정보를 기반으로 이름을 지어야한다. 불용어를 추가하면 안된다. 

컴파일러나 인터프리터만 통과하려는 생각으로 코드를 구현하면 안된다.

예를 들면 연속적인 숫자를 덧붙인 이름 (a1, a2, a3 ... )로 지으면 안된다. 아무런 정보를 나타낼 수 없기 떄문이다.

ProductInfo, ProductData라는 이름으로 지으면 안된다. 이미 Product라는 이름으로 충분한 의미를 제공하고 있는데 굳이 중복해서 Info와 Data를 붙일 필요가 없다. 

NameString과 같은 이름도 지으면 안된다. Name과 굳이 차이가 있을까? 

***
## Principle 4. 발음하기 쉬운 이름을 사용해라

프로그래밍은 사회 활동이다. 서로 의논하기 쉽게 발음하기 쉬운 이름을 사용하는게 좋다. 


***

## Principle 5. 검색하기 쉬운 이름을 사용해라 

문자 하나를 사용하는 이름 또는 상수는 코드에서 검색하기 힘들다. 

이런 경우들은 검색을 하면 여러가지 경우의 수와 연관해서 볼 수 있다. 즉 찾아서 고치기 힘들다. 

개인적으로는 간단한 메소드에서 로컬 변수의 경우에만 한 문자를 사용하면 좋다. 

이름 길이는 범위 크기에 비례해야한다.

***

## Principle 6. 인코딩을 피해라 

굳이 부담을 더하지 않아도 이름에 인코딩할 정보는 충분히 많다. 그러므로 접두어를 통해 범위 정보까지 인코딩에 넣지말자. 

### 인터페이스 클래스와 구현 클래스 

때로는 인코딩이 필요한 경우도 있다. 인터페이스 클래스와 구현 클래스 이렇게 있다고 생각해보자. 

예를 들어 도형을 생성하는 클래스를 만든다고 하면 인터페이스는 IShapeFactory 이렇게 짓고 구현 클래스는 ShapeFactory로 지으면 될까? 

개인적으로는 인터페이스 이름에 괜히 접두어를 넣을 필요는 없다 

하지만 인터페이스는 ShapeFactory 구현 클래스는 ShapeFactoryImpl 이렇게 지으는건 개인 취향이라고 생각한다.

***

## Principle 7. 자신의 기억력을 과시하지 마라 

코드를 읽는 사람이 읽으면서 변수 이름을 자신이 아는 이름으로 변환해야 한다면 좋지 않은 이름이다. 

이는 일반적으로 이해하기 어려운 이름을 뜻환다. 

한 글자의 이름들 i,j,k은 루프에서만 쓰자. 

기발한 이름과 잘 사용되지 않은 단어를 쓰는 이름은 피하자. 

이름은 간단하고 명료할수록 좋다. 

클래스 이름은 명사구로 쓰고 메소드의 이름은 동사구로 쓰자. 

그리고 생성자를 오버로딩 하는 일이 있다면 그 생성자의 인수들을 설명해주는 스태틱 메소드를 만들어서 사용하자.

***

## Principle 8. 해법 영역에서 가져온 이름을 사용해라 

코드를 읽을 사람도 프로그래머라는 사실을 명시한다.

그러므로 전산 용어, 알고리즘 이름, 패턴 이름, 수학 용어 이름도 괜찮다. 

즉 VISITOR 패턴에 익숙한 사람에게 AccountVisitor라는 이름은 금방 이해한다. 

***

## Principle 9. 문제 영역에서 가져온 이름을 사용해라 

적절한 프로그래머 용어가 없다면 문제 영역에서 이름을 가져오면 좋다. 

그러면 코드를 보수하는 프로그래머가 전문가에게 의미를 물어 파악할 수 있다.

***

## Principle 10. 의미있는 맥락을 추가해라

스스로 의미가 분명한 이름은 있다.

하지만 그렇지 못한 이름도 있다.

예를 들어 firstName, lastName, address, state, city, zipcode와 같은 맥락이 있다면 state가 무슨 의미인지 알 수 있다.

하지만 state만 있다면 이게 무슨 의미인지 알기 힘들다.

다음과 같은 예를 보자.

```java
	private void printGuessStatistics(char candidate, int count) {
    	String number;
        String verb;
        String pluralModifier; 
        
        if(count == 0){
        	number = "no";
            verb = "are";
            pluralModifier = "s"; 
        }else if(count == 1){
        	number = "1";
            verb = "is";
            pluralMo
        }else {
        	number = Integer.toString(count);
            verb = "are";
            pluralModifier = "s";
        }
       ...
    }
```

이 예는 일단 함수가 좀 길다. 그리고 세 변수를 함수 함수 전반에서 사용한다. 

물론 함수 이름이 설명해주지만 이 함수를 쭉 읽고 나서야 이 세 변수가 통계 추측에 사용되는 걸 알 수 있다. 

하지만 이 변수를 포함한 GuessStatisticMessage 라는 클래스를 새로 정의하면 통계 추측에 사용된다는 맥락을 줄 수 있다. 

다음과 같이 말이다.

***

## Principle 11. 불필요한 맥락을 없애라

넓은 맥락을 가진 이름은 사용하지 않는게 좋다. 나중에 중복될 수도 있기도 하고 정확한 설명이 아니기 때문이다.

예를 들어 Address 클래스의 인스턴스로 accountAddress 보다는 customerAddress 라는 이름이 더 낫다. 

***















