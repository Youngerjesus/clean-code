> __클린 코드 Chapter 14. 점진적인 개선에 대해 정리합니다.__
>
> __학습할 내용은 다음과 같습니다.__
> - Intro  
> - Args 구현
> - Args 1차 초안
> - 결론 
> 
> __Reference__
>
> - [Clean Code 클린 코드 애자일 소프트웨어 장인 정신](http://www.yes24.com/Product/Goods/11681152)

***

## Intro 

여기서는 점진적인 개선을 실제 사례를 보여주는 장이다. 

시작은 좋았으나 확장성이 부족했던 모듈을 소개하고 어떻게 점진적으로 모듈을 개선하고 정리하는지 보여주는지를 살펴보자. 

점진적인 개선을 할 프로그램은 명령행 인수의 구문을 분석하는 프로그램이다. 

이 프로그램은 main 함수로 넘어오는 문자열을 직접 분석할 필요 없는 유틸리티 함수다. 이 유틸리티의 이름을 Args라고 하겠다. 

Args 사용방법은 간단하다. 

Args 생성자에 인수 문자열과 형식 문자열을 넘겨서 Args 인스턴스를 생성한 후 질의할 인수 값을 넘기면 된다. 다음 예를 보자.

```java
public static void main(String[] args) {
  try {
    Args arg = new Args("l,p#,d*", args);
    boolean logging = arg.getBoolean('l');
    int port = arg.getInt('p');
    String directory = arg.getString('d');
    executeApplication(logging, port, directory);
  } catch (ArgsException e) {
    System.out.print("Argument error: %s\n", e.errorMessage());
  }
}
```

매개변수 두개로 Args 클래스 사용방법이 들어난다. 

첫째 매겨변수는 형식 또는 스키마를 지정하는 "l,p#,d*"이다. 

첫 번째 l은 boolean 인수이고 두 번째 p는 정수 인수다. 세 번째 d는 문자열 인수다. Args 생성자로 넘긴 둘째 매개변수는 main으로 넘어온 명령행 인수 배열 자체다. 

인수 값을 가져오기 위해서 getBoolean, getInteger, getString 등과 같은 메소드를 사용해서 가져올 수 있다. 

형식 문자열이나 명령행 인수 자체에 문제가 있다면 ArgsException이 발생한다. 

그리고 구체적인 오류를 알아내려면 예외가 제공하는 errorMessage 메소드를 사용한다. 

***

## Args 구현

디음 예는 완성된 Args 클래스다. 

##### Args.java
```java
package com.objectmentor.utilities.args;

import static com.objectmentor.utilities.args.ArgsException.ErrorCode.*; 
import java.util.*;

public class Args {
  private Map<Character, ArgumentMarshaler> marshalers;
  private Set<Character> argsFound;
  private ListIterator<String> currentArgument;
  
  public Args(String schema, String[] args) throws ArgsException { 
    marshalers = new HashMap<Character, ArgumentMarshaler>(); 
    argsFound = new HashSet<Character>();
    
    parseSchema(schema);
    parseArgumentStrings(Arrays.asList(args)); 
  }
  
  private void parseSchema(String schema) throws ArgsException { 
    for (String element : schema.split(","))
      if (element.length() > 0) 
        parseSchemaElement(element.trim());
  }
  
  private void parseSchemaElement(String element) throws ArgsException { 
    char elementId = element.charAt(0);
    String elementTail = element.substring(1); validateSchemaElementId(elementId);
    if (elementTail.length() == 0)
      marshalers.put(elementId, new BooleanArgumentMarshaler());
    else if (elementTail.equals("*")) 
      marshalers.put(elementId, new StringArgumentMarshaler());
    else if (elementTail.equals("#"))
      marshalers.put(elementId, new IntegerArgumentMarshaler());
    else if (elementTail.equals("##")) 
      marshalers.put(elementId, new DoubleArgumentMarshaler());
    else if (elementTail.equals("[*]"))
      marshalers.put(elementId, new StringArrayArgumentMarshaler());
    else
      throw new ArgsException(INVALID_ARGUMENT_FORMAT, elementId, elementTail);
  }
  
  private void validateSchemaElementId(char elementId) throws ArgsException { 
    if (!Character.isLetter(elementId))
      throw new ArgsException(INVALID_ARGUMENT_NAME, elementId, null); 
  }
  
  private void parseArgumentStrings(List<String> argsList) throws ArgsException {
    for (currentArgument = argsList.listIterator(); currentArgument.hasNext();) {
      String argString = currentArgument.next(); 
      if (argString.startsWith("-")) {
        parseArgumentCharacters(argString.substring(1)); 
      } else {
        currentArgument.previous();
        break; 
      }
    } 
  }
  
  private void parseArgumentCharacters(String argChars) throws ArgsException { 
    for (int i = 0; i < argChars.length(); i++)
      parseArgumentCharacter(argChars.charAt(i)); 
  }
  
  private void parseArgumentCharacter(char argChar) throws ArgsException { 
    ArgumentMarshaler m = marshalers.get(argChar);
    if (m == null) {
      throw new ArgsException(UNEXPECTED_ARGUMENT, argChar, null); 
    } else {
      argsFound.add(argChar); 
      try {
        m.set(currentArgument); 
      } catch (ArgsException e) {
        e.setErrorArgumentId(argChar);
        throw e; 
      }
    } 
  }
  
  public boolean has(char arg) { 
    return argsFound.contains(arg);
  }
  
  public int nextArgument() {
    return currentArgument.nextIndex();
  }
  
  public boolean getBoolean(char arg) {
    return BooleanArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public String getString(char arg) {
    return StringArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public int getInt(char arg) {
    return IntegerArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public double getDouble(char arg) {
    return DoubleArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public String[] getStringArray(char arg) {
    return StringArrayArgumentMarshaler.getValue(marshalers.get(arg));
  } 
}
```

여기저기 찾아볼 필요 없이 코드가 읽힐것이다. 

한 가지 먼저 읽어볼 코드가 있다면 ArgumentMarshaler 정의인데 이 코드는 바로 이후에 설명하겠다. 

코드를 주의깊게 읽어봤다면 ArgumentMarshaler 인터페이스가 무슨 역할을 하는지 이해할 수 있을것이다. 

다음 예는 ArgumentMarshaler 인터페이스와 그 구현체들이다.


##### ArgumentMarshaler.java
```java
public interface ArgumentMarshaler {
  void set(Iterator<String> currentArgument) throws ArgsException;
}
```

##### BooleanArgumentMarshaler.java

```java
public class BooleanArgumentMarshaler implements ArgumentMarshaler { 
  private boolean booleanValue = false;
  
  public void set(Iterator<String> currentArgument) throws ArgsException { 
    booleanValue = true;
  }
  
  public static boolean getValue(ArgumentMarshaler am) {
    if (am != null && am instanceof BooleanArgumentMarshaler)
      return ((BooleanArgumentMarshaler) am).booleanValue; 
    else
      return false; 
  }
}
```

##### StringArgumentMarshaler.java

```java
import static com.objectmentor.utilities.args.ArgsException.ErrorCode.*;

public class StringArgumentMarshaler implements ArgumentMarshaler { 
  private String stringValue = "";
  
  public void set(Iterator<String> currentArgument) throws ArgsException { 
    try {
      stringValue = currentArgument.next(); 
    } catch (NoSuchElementException e) {
      throw new ArgsException(MISSING_STRING); 
    }
  }
  
  public static String getValue(ArgumentMarshaler am) {
    if (am != null && am instanceof StringArgumentMarshaler)
      return ((StringArgumentMarshaler) am).stringValue; 
    else
      return ""; 
  }
}
```

#####  IntegerArgumentMarshaler.java

```java
import static com.objectmentor.utilities.args.ArgsException.ErrorCode.*;

public class IntegerArgumentMarshaler implements ArgumentMarshaler { 
  private int intValue = 0;
  
  public void set(Iterator<String> currentArgument) throws ArgsException { 
    String parameter = null;
    try {
      parameter = currentArgument.next();
      intValue = Integer.parseInt(parameter);
    } catch (NoSuchElementException e) {
      throw new ArgsException(MISSING_INTEGER);
    } catch (NumberFormatException e) {
      throw new ArgsException(INVALID_INTEGER, parameter); 
    }
  }
  
  public static int getValue(ArgumentMarshaler am) {
    if (am != null && am instanceof IntegerArgumentMarshaler)
      return ((IntegerArgumentMarshaler) am).intValue; 
    else
    return 0; 
  }
}
```

나머지 두개 DoubleArgumentMarshaler와 StringArrayArgumentMarshaler도 있지만 어짜피 구현 패턴은 같으므로 생략하겠다. 

한 가지 더 소개할 부분이 있는데 ArgumentMarshaler에서 예외로 던지는 부분인 ArgsException이다. 

여기서 오류코드를 정의하는 부분이 거슬릴 수 있다. 

##### ArgsException.java

```java
import static com.objectmentor.utilities.args.ArgsException.ErrorCode.*;

public class ArgsException extends Exception { 
  private char errorArgumentId = '\0'; 
  private String errorParameter = null; 
  private ErrorCode errorCode = OK;
  
  public ArgsException() {}
  
  public ArgsException(String message) {super(message);}
  
  public ArgsException(ErrorCode errorCode) { 
    this.errorCode = errorCode;
  }
  
  public ArgsException(ErrorCode errorCode, String errorParameter) { 
    this.errorCode = errorCode;
    this.errorParameter = errorParameter;
  }
  
  public ArgsException(ErrorCode errorCode, char errorArgumentId, String errorParameter) {
    this.errorCode = errorCode; 
    this.errorParameter = errorParameter; 
    this.errorArgumentId = errorArgumentId;
  }
  
  public char getErrorArgumentId() { 
    return errorArgumentId;
  }
  
  public void setErrorArgumentId(char errorArgumentId) { 
    this.errorArgumentId = errorArgumentId;
  }
  
  public String getErrorParameter() { 
    return errorParameter;
  }
  
  public void setErrorParameter(String errorParameter) { 
    this.errorParameter = errorParameter;
  }
  
  public ErrorCode getErrorCode() { 
    return errorCode;
  }
  
  public void setErrorCode(ErrorCode errorCode) { 
    this.errorCode = errorCode;
  }
  
  public String errorMessage() { 
    switch (errorCode) {
      case OK:
        return "TILT: Should not get here.";
      case UNEXPECTED_ARGUMENT:
        return String.format("Argument -%c unexpected.", errorArgumentId);
      case MISSING_STRING:
        return String.format("Could not find string parameter for -%c.", errorArgumentId);
      case INVALID_INTEGER:
        return String.format("Argument -%c expects an integer but was '%s'.", errorArgumentId, errorParameter);
      case MISSING_INTEGER:
        return String.format("Could not find integer parameter for -%c.", errorArgumentId);
      case INVALID_DOUBLE:
        return String.format("Argument -%c expects a double but was '%s'.", errorArgumentId, errorParameter);
      case MISSING_DOUBLE:
        return String.format("Could not find double parameter for -%c.", errorArgumentId); 
      case INVALID_ARGUMENT_NAME:
        return String.format("'%c' is not a valid argument name.", errorArgumentId);
      case INVALID_ARGUMENT_FORMAT:
        return String.format("'%s' is not a valid argument format.", errorParameter);
    }
    return ""; 
  }
  
  public enum ErrorCode {
    OK, INVALID_ARGUMENT_FORMAT, UNEXPECTED_ARGUMENT, INVALID_ARGUMENT_NAME, 
    MISSING_STRING, MISSING_INTEGER, INVALID_INTEGER, MISSING_DOUBLE, INVALID_DOUBLE
  }
}
```

개념에 비해 코드가 되게 장황하다고 생각할 수 있지만 이는 자바가 정적 타입 지원이라서 그렇다. 

루비나 파이썬을 사용했다면 훨씬 코드의 길이는 줄었을 것이다. 

여기애 있는 코드는 전반적으로 깔끔한 구조이다. 

예를 들어 날짜 인수나 복소수 인수같은 새로운 형식을 지원하고 싶다면 ArgumentMarshaler 인터페이스를 구현한 새 클래스를 만들어서 사용하고 getXXX() 메소드를 추가하면 된다. 

그 후 parseSchemaElement에 case를 추가하면 된다. 

필요한 에러코드가 생긴다면 ArgsException에 새로운 에러 메시지를 추가하면 된다.

### 어떻게 짰는가? 

위 프로그램을 한번에 짜기 힘들다. 그래서 점진적인 개선이 필요한 이유다. 

깨끗한 코드를 짜려면 지저분한 코드를 짠 후에 정리해야 한다는 의미다. 

이런 이야기는 처음 듣지는 않았을 것이다. 어릴 때 글쓰는 것과 유사하게 초안을 짠 후 그 초안을 고쳐서 2차 초안을 만들고 계속해서 최종안을 만들면 된다. 깔끔한 작품을 내놓으려면 단계적으로 개선해야 한다. 

대다수 프로그래머는 이 규칙을 따르지 않는다. 

그들의 목적은 돌아가는 프로그램이다. 돌아가기만 하면 다음 업무로 뛰어든다. 경험이 풍부한 프로그래머는 이런 행동이 얼마나 심각한지 이해하고 있다.


***

## Args 1차 초안

다음은 Args 1차 초안이다. 코드는 돌아가지만 엉망이다.

##### Args.java (1차 초안)

```java
import java.util.*;

public class Args {
  private String schema;
  private String[] args;
  private boolean valid = true;
  private Set<Character> unexpectedArguments = new TreeSet<Character>(); 
  private Map<Character, Boolean> booleanArgs = new HashMap<Character, Boolean>();
  private Map<Character, String> stringArgs = new HashMap<Character, String>(); 
  private Map<Character, Integer> intArgs = new HashMap<Character, Integer>(); 
  private Set<Character> argsFound = new HashSet<Character>();
  private int currentArgument;
  private char errorArgumentId = '\0';
  private String errorParameter = "TILT";
  private ErrorCode errorCode = ErrorCode.OK;
  
  private enum ErrorCode {
    OK, MISSING_STRING, MISSING_INTEGER, INVALID_INTEGER, UNEXPECTED_ARGUMENT}
    
  public Args(String schema, String[] args) throws ParseException { 
    this.schema = schema;
    this.args = args;
    valid = parse();
  }
  
  private boolean parse() throws ParseException { 
    if (schema.length() == 0 && args.length == 0)
      return true; 
    parseSchema(); 
    try {
      parseArguments();
    } catch (ArgsException e) {
    }
    return valid;
  }
  
  private boolean parseSchema() throws ParseException { 
    for (String element : schema.split(",")) {
      if (element.length() > 0) {
        String trimmedElement = element.trim(); 
        parseSchemaElement(trimmedElement);
      } 
    }
    return true; 
  }
  
  private void parseSchemaElement(String element) throws ParseException { 
    char elementId = element.charAt(0);
    String elementTail = element.substring(1); 
    validateSchemaElementId(elementId);
    if (isBooleanSchemaElement(elementTail)) 
      parseBooleanSchemaElement(elementId);
    else if (isStringSchemaElement(elementTail)) 
      parseStringSchemaElement(elementId);
    else if (isIntegerSchemaElement(elementTail)) 
      parseIntegerSchemaElement(elementId);
    else
      throw new ParseException(String.format("Argument: %c has invalid format: %s.", 
        elementId, elementTail), 0);
    } 
  }
    
  private void validateSchemaElementId(char elementId) throws ParseException { 
    if (!Character.isLetter(elementId)) {
      throw new ParseException("Bad character:" + elementId + "in Args format: " + schema, 0);
    }
  }
  
  private void parseBooleanSchemaElement(char elementId) { 
    booleanArgs.put(elementId, false);
  }
  
  private void parseIntegerSchemaElement(char elementId) { 
    intArgs.put(elementId, 0);
  }
  
  private void parseStringSchemaElement(char elementId) { 
    stringArgs.put(elementId, "");
  }
  
  private boolean isStringSchemaElement(String elementTail) { 
    return elementTail.equals("*");
  }
  
  private boolean isBooleanSchemaElement(String elementTail) { 
    return elementTail.length() == 0;
  }
  
  private boolean isIntegerSchemaElement(String elementTail) { 
    return elementTail.equals("#");
  }
  
  private boolean parseArguments() throws ArgsException {
    for (currentArgument = 0; currentArgument < args.length; currentArgument++) {
      String arg = args[currentArgument];
      parseArgument(arg); 
    }
    return true; 
  }
  
  private void parseArgument(String arg) throws ArgsException { 
    if (arg.startsWith("-"))
      parseElements(arg); 
  }
  
  private void parseElements(String arg) throws ArgsException { 
    for (int i = 1; i < arg.length(); i++)
      parseElement(arg.charAt(i)); 
  }
  
  private void parseElement(char argChar) throws ArgsException { 
    if (setArgument(argChar))
      argsFound.add(argChar); 
    else 
      unexpectedArguments.add(argChar); 
      errorCode = ErrorCode.UNEXPECTED_ARGUMENT; 
      valid = false;
  }
  
  private boolean setArgument(char argChar) throws ArgsException { 
    if (isBooleanArg(argChar))
      setBooleanArg(argChar, true); 
    else if (isStringArg(argChar))
      setStringArg(argChar); 
    else if (isIntArg(argChar))
      setIntArg(argChar); 
    else
      return false;
    
    return true; 
  }
  
  private boolean isIntArg(char argChar) {
    return intArgs.containsKey(argChar);
  }
  
  private void setIntArg(char argChar) throws ArgsException { 
    currentArgument++;
    String parameter = null;
    try {
      parameter = args[currentArgument];
      intArgs.put(argChar, new Integer(parameter)); 
    } catch (ArrayIndexOutOfBoundsException e) {
      valid = false;
      errorArgumentId = argChar;
      errorCode = ErrorCode.MISSING_INTEGER;
      throw new ArgsException();
    } catch (NumberFormatException e) {
      valid = false;
      errorArgumentId = argChar; 
      errorParameter = parameter;
      errorCode = ErrorCode.INVALID_INTEGER; 
      throw new ArgsException();
    } 
  }
  
  private void setStringArg(char argChar) throws ArgsException { 
    currentArgument++;
    try {
      stringArgs.put(argChar, args[currentArgument]); 
    } catch (ArrayIndexOutOfBoundsException e) {
      valid = false;
      errorArgumentId = argChar;
      errorCode = ErrorCode.MISSING_STRING; 
      throw new ArgsException();
    } 
  }
  
  private boolean isStringArg(char argChar) { 
    return stringArgs.containsKey(argChar);
  }
  
  private void setBooleanArg(char argChar, boolean value) { 
    booleanArgs.put(argChar, value);
  }
  
  private boolean isBooleanArg(char argChar) { 
    return booleanArgs.containsKey(argChar);
  }
  
  public int cardinality() { 
    return argsFound.size();
  }
  
  public String usage() { 
    if (schema.length() > 0)
      return "-[" + schema + "]"; 
    else
      return ""; 
  }
  
  public String errorMessage() throws Exception { 
    switch (errorCode) {
      case OK:
        throw new Exception("TILT: Should not get here.");
      case UNEXPECTED_ARGUMENT:
        return unexpectedArgumentMessage();
      case MISSING_STRING:
        return String.format("Could not find string parameter for -%c.", errorArgumentId);
      case INVALID_INTEGER:
        return String.format("Argument -%c expects an integer but was '%s'.", errorArgumentId, errorParameter);
      case MISSING_INTEGER:
        return String.format("Could not find integer parameter for -%c.", errorArgumentId);
    }
    return ""; 
  }
  
  private String unexpectedArgumentMessage() {
    StringBuffer message = new StringBuffer("Argument(s) -"); 
    for (char c : unexpectedArguments) {
      message.append(c); 
    }
    message.append(" unexpected.");
    
    return message.toString(); 
  }
  
  private boolean falseIfNull(Boolean b) { 
    return b != null && b;
  }
  
  private int zeroIfNull(Integer i) { 
    return i == null ? 0 : i;
  }
  
  private String blankIfNull(String s) { 
    return s == null ? "" : s;
  }
  
  public String getString(char arg) { 
    return blankIfNull(stringArgs.get(arg));
  }
  
  public int getInt(char arg) {
    return zeroIfNull(intArgs.get(arg));
  }
  
  public boolean getBoolean(char arg) { 
    return falseIfNull(booleanArgs.get(arg));
  }
  
  public boolean has(char arg) { 
    return argsFound.contains(arg);
  }
  
  public boolean isValid() { 
    return valid;
  }
  
  private class ArgsException extends Exception {
  } 
}
```

1차 초안은 명백히 미완성이다. 인스턴스 변수 개수만도 압도적이다.

이 코드도 처음부터 지저분하게 코드를 짜려는 생각이 있었던 건 아니다. 실제로 코드를 어느정도 손보면서 짠게 이정도다. 

함수 이름이나 변수 이름을 선택한 방식이 그 증거다. 

하지만 그 구조가 점점 커저나가면서 프로그램은 내 손을 벗어나게 되고 코드는 엉망이 되간다. 

다음 예를 보자. 이 초안의 초기 버전으로 Boolean 인수만 지원하던 초기 버전이다.

##### Args.java (Boolean만 지원하는 버전)

```java
import java.util.*;

public class Args {
  private String schema;
  private String[] args;
  private boolean valid = true;
  private Set<Character> unexpectedArguments = new TreeSet<Character>(); 
  private Map<Character, Boolean> booleanArgs = new HashMap<Character, Boolean>();
  private int numberOfArguments = 0;
  
  public Args(String schema, String[] args) throws ParseException { 
    this.schema = schema;
    this.args = args;
    valid = parse();
  }
  
  private boolean parse() throws ParseException { 
    if (schema.length() == 0 && args.length == 0)
      return true; 
    parseSchema(); 
    try {
      parseArguments();
    } catch (ArgsException e) {
    }
    return valid;
  }
  
  private boolean parseSchema() throws ParseException { 
    for (String element : schema.split(",")) {
      if (element.length() > 0) {
        parseSchemaElement(trimmedElement);
      } 
    }
    return true; 
  }
  
  private void parseSchemaElement(String element) { 
  	if(element.length() == 1){
    	parseBooleanSchemaElement(element); 
    }
  }
    
  
  private void parseBooleanSchemaElement(char elementId) { 
    booleanArgs.put(elementId, false);
  }
  
  private boolean isBooleanSchemaElement(String elementTail) { 
    return elementTail.length() == 0;
  }
  
  
  private boolean parseArguments() {
    for (String arg : args) {
      parseArgument(arg); 
    }
    return true; 
  }
  
  private void parseArgument(String arg) { 
    if (arg.startsWith("-"))
      parseElements(arg); 
  }
  
  private void parseElements(String arg)  { 
    for (int i = 1; i < arg.length(); i++)
      parseElement(arg.charAt(i)); 
  }
  
  private void parseElement(char argChar)  { 
    if (isBoolean(argChar))
      numberOfArguments++;
      setBooleanArg(argChar, true); 
    else 
      unexpectedArguments.add(argChar); 
  }
  
  
  private void setBooleanArg(char argChar, boolean value) { 
    booleanArgs.put(argChar, value);
  }
  
  private boolean isBooleanArg(char argChar) { 
    return booleanArgs.containsKey(argChar);
  }
  
  public int cardinality() { 
    return numberOfArguments; 
  }
  
  public String usage() { 
    if (schema.length() > 0)
      return "-[" + schema + "]"; 
    else
      return ""; 
  }
  
  public String errorMessage() throws Exception { 
    if(unexpectedArgument.size() > 0){
    	return unexpectedArgumentMessage(); 
    } else
    	return ""; 
  }
  
  private String unexpectedArgumentMessage() {
    StringBuffer message = new StringBuffer("Argument(s) -"); 
    for (char c : unexpectedArguments) {
      message.append(c); 
    }
    message.append(" unexpected.");
    
    return message.toString(); 
  }
  
  public boolean getBoolean(char arg) { 
    return falseIfNull(booleanArgs.get(arg));
  }
  
  
  
  public boolean isValid() { 
    return valid;
  }
}

```

위 코드도 불평 거리가 많겠지만 나름 괜찮은 코드다.

간결하고 단순하며 이해하기 쉽다. 하지만 코드를 잘 살펴보면 나중에 엉망으로 변해갈 씨앗이 보인다.

코드가 지저분해진 이유가 들어난다. 

여기서 String과 Integer 예만 추가했을 뿐인데 코드가 엄청나게 지저분해질 수 있다.
 
__리팩토링 타이밍은 이런 경우에 해야한다.__ 

추가할 유형은 String과 Integer 이외에 Double과 StringArray등 더 있다. 

하지만 이런 유형을 추가하면 코드는 엄청 더 나빠질 것이라는 사실은 명백하다. 

String과 Integer 유형을 추가할 경우에 주요 지점 세 곳ㅇ레다 코드를 추가해야 한다는 사실을 알고 있었다.

첫째 인수 유형에 해당하는 HashMap을 선택하기 위해 스키마 요소의 구문을 분석한다. 

둘째 명령행 인수에서 인수 유형을 분석해 진짜 인수 유형으로 변환한다.

셋째 getXXX 메소드를 구현해 호출자에게 진짜 유형을 반환한다. 

인수 유형은 다양하지만 모두 유사한 메소드를 제공하므로 클래스 하나가 적합하다고 판단했다. 역할과 구현으로 나눈 것

그래서 ArgumentMarshaler라는 개념이 탄생했다. 

### 점진적으로 개선하다.

프로그램을 망치는 가장 좋은 방법 중 하나는 개선이라는 이름 아래 구조를 크게 바꾸는 것이다. 

어떤 프로그래임은 개선에서 결코 회복하지 못한다. 왜냐하면 프로그램을 다시 돌려서 제대로 동작하는지 확인하기가 어렵기 떄문이다.

그래서 TDD(Test-Driven Development)이라는 기법을 사용해야한다. 

TDD는 언제 어느 때라도 시스템이 돌아가야 한다는 원칙을 따른다. 다시말해 TDD는 시스템을 망가뜨리는 변경을 허용하지 않는다. 

변경을 가한 후에도 시스템이 변경 전과 똑같이 돌아가야 한다는 원칙을 따른다. 

TDD를 따르기 위해서는 변경 전과 변경 후가 똑같이 돌아가햐 한다는 사실을 알기 위해 자동화된 테스트 슈트가 필요하다. 

앞서 Args 클래스를 구현하는 동안에 이미 단위 테스트 슈트와 인수 테스트를 만들어 놓았다. 두 테스트 모두 언제든 실행이 가능했고, 시스템이 두 테스트를 모두 통과하면 올바로 동작한다고 봐도 좋았다.

이제 시스템에 자잘한 변경을 가하기 시작했다. 코드를 변경할 때마다 시스템 구조는 조금씩 ArgumentMarshaler 개념에 가까워졌다. 또한 변경 후에도 시스템은 여전히 잘 돌아갔다. 가장 먼저 기존 코드 끝에 ArgumentMarshaler 클래스의 골격을 추가했다. 

우선 boolean 부분 부터 해보면 다음과 같다. 

##### Args.java 끝에 추가한 ArgumentMarshaler

```java
private class ArgumentMarshaler { 
  private boolean booleanValue = false;

  public void setBoolean(boolean value) { 
    booleanValue = value;
  }
  
  public boolean getBoolean() {return booleanValue;} 
}

private class BooleanArgumentMarshaler extends ArgumentMarshaler { }
private class StringArgumentMarshaler extends ArgumentMarshaler { }
private class IntegerArgumentMarshaler extends ArgumentMarshaler { }
```

그리고 코드를 최소로 건드리는, 가장 단순한 변경을 가했다. 구체적으로는 Boolean 인수를 저장하는 HashMap에서 Boolean 인수 유형을 ArgumentMarshaler 유형으로 바꿨다.

```java
private Map<Character, ArgumentMarshaler> boolean Args = new HashMap<Character, ArgumentMarshaler>();
```

이런식으로 점진적인 개선을 할 수 있다. 

## 결론

소프트웨어 설계는 분할만 잘해도 품질이 크게 높아진다. 

적절한 장소를 만들어 코드만 분리해도 설계가 좋아진다. 관심사를 분리하면 코드를 이해하고 보수하기 훨씬 더 쉬워진다.

그리고 그저 돌아가는 코드만으로는 붖고하다. 

돌아가는 코드가 심하게 망가지는 사례는 흔하다. 단순히 돌아가는 코드에 만족하는 프로그래머는 전문가 정신이 부족하다. 

설계와 구조를 개선할 시간이 없다고 변명할지 모르지만 동의하기는 어렵다. 

나쁜 코드보다 더 오랫동안 더 심각하게 개발 프로젝트에 악영향을 미치는 요인도 없다. 나쁜 일정은 다시 짜면 된다. 

그러므로 코드는 언제나 최대한 깔끔하고 단순하게 정리하자. 절대로 썩어가게 방치하면 안된다. 



