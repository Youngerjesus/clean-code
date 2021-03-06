> __클린 코드 Chapter 8. 경계에 대해 정리합니다.__
>
> __학습할 내용은 다음과 같습니다.__
> - Principle 1. 외부 코드 사용하기 
> - Principle 2. 경계 살피고 익히기 
> - Principle 3. 학습 테스트는 공짜 이상이다.
> - Principle 4. 깨끗한 경계
>
> __Reference__
>
> - [Clean Code 클린 코드 애자일 소프트웨어 장인 정신](http://www.yes24.com/Product/Goods/11681152)

***

## Intro 

시스템에 들어가는 모든 소프트웨어를 직접 개발하는 경우는 드물다. 

때로는 패키지를 쓰고 떄로는 오픈소스를 이용한다. 

어떤 식으로든 이 외부 코드를 우리 코드에 깔끔하게 통합해서 사용해야한다. 

이 장에서는 소프트웨어의 경계를 깔끔하게 처리하는 방법에대해 알아보겠다.

***

## Principle 1. 외부 코드 사용하기 

패키지 제공자나 인터페이스 제공자는 적용성을 최대한 넓혀서 제공해준다. 

반면에 사용자는 자신의 요구사항에 맞게 사용하도록 바란다. 

그러므로 이런 경계가 있는 인터페이스들은 여기저기 넘기지 마라. 

여기저기 넘겨질 구조라면 캡슐화를 해서 필요한 기능만 제공하도록 해라.

예로 java.util.map 의 경우 clear() 메소드가 있다. 즉 이 map을 받는 사람이라면 누구든지 지울 권리가 있다는 뜻이다. 

그러므로 그냥 map을 제공해서 주지 말고 캡슐화를 해서 clear()을 못하도록 필요한 기늠만 만들어서 제공해줘라. 

***

## Principle 2. 경계 살피고 익히기 

외부 코드를 사용하면 적은 시간에 더 많은 기능을 출시하기 쉬워진다. 

패키지 테스트가 우리 책임은 아니지만 우리를 위해서 테스트 하는걸 권장한다.

그리고 하루 이틀 (아니면 더 오랫동안) 문서를 읽으면서 사용방법을 익히길 권한다. 

즉 코드를 작성해 외부 코드를 호출하기 보단 먼저 간단한 테스트 케이스를 통해 외부 코드 사용법을 익히라는 말이다. 

이를 학습 테스트라고도 부른다. 

***


## Principle 3. 학습 테스트는 공짜 이상이다. 

학습 테스트는 공짜 이상이다. 투자하는 노력보다 얻는 성과가 더 크다. 

패키지 새 버전이 나온다면 학습 테스트를 돌려서 차이가 있는지 확인한다. 

새 버전이 이전 버전과 달리 호환된다는 보장이 없다. 

이렇게 한다면 새 버전으로 이전하기도 쉽다.

***


## Principle 4. 깨끗한 경계

통제하지 못하는 코드를 사용할땐 향후 변경 비용이 지나치게 크지 않게 주의하라.

경계에 위치하는 코드는 깔끔하게 분리해야한다.

그리고 외부 패키지에 의존하는 대신 통제가 가능한 우리코드에 의존하도록 한다. 

외부 패키지를 호출하는 코드를 가능한 줄여서 경계를 관리하자.









