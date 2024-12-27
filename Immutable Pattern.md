# Immutable 패턴
- 한번 만든 객체를 변형하지 않고, 변형이 발생할 때마다 새로운 객체를 만드는 방식.<br/><br/>

### 1. 개념
- 객체의 상태를 생성 후, 변경할 수 없도록 하는 디자인 패턴 <br><br>
- 객체 생성 후, 그 객체의 상태(필드 값)를 변경하는 메소드를 제공하지 않으며, 모든 필드는 초기화 시에만 설정되고 <br>
  이후, 읽기 전용으로만 접근 가능. <br><br>
- 멀티스레드 환경에서 데이터의 안전성을 보장하고 객체의 일관성을 유지하는데 매우 유용.

<br>

### 2. 등장배경
- 객체의 상태를 변경할 수 없기 때문에 멀티스레드 환경에서 동기화 문제가 발생하지 않음. <br>
  또한, 객체가 불변이기 때문에 객체가 공유될 때에도 다른 객체나 스레드에서 그 객체의 상태를 변경할 수 없어 안전함 <br>
  특히, 상태 변경이 필요 없는 데이터 객체를 생성 시 유용.

<br>

### 3. 언제 사용하면 좋을까?
- 객체의 상태가 변경되지 않아야 할 경우
- 멀티스레드 환경에서 데이터 일관성을 보장하고 싶을 때
- 객체의 상태 변경이 없으므로 객체의 해시 코드가 동일하게 유지되어야 할 때
- 컬렉션 객체, 날짜/시간 객체, 설정 객체 등 불변성이 필요한 데이터 객체를 처리할 때

<br>

### 4. 활용 예시
- Java의 String 클래스 
  - `String`은 불변 객체로 설계되어 한번 생성된 `String`객체는 변경 불가능 <br><br>

- 날짜와 시간 객체
  - `LocalDate`, `LocalTime`, `LocalDateTime` 클래스는 모두 불변 객체. <br>
    한번 설정된 날짜나 시간 값은 변경 불가능. <br><br>

- 설정 값 객체
  - 설정 정보를 담고 있는 객체는 불변으로 만들어서 초기화 후 변경되지 않도록 보장가능


<br>

### 5. 장점
- 멀티스레드 환경에서 안정성
  - 불변 객체는 여러 스레드에서 동시에 접근하더라도 상태가 변경되지 않기 때문에 동기화에 대한 추가 처리 불필요 <br><br>
  
- 예측 가능성
  - 객체의 상태 변경이 되지 않으므로, 객체를 사용하는 코드가 더 예측 가능하고 디버깅에 용이 <br><br>
    
- 불변성으로 인한 버그 감소
  - 객체의 상태를 변경할 수 없기 때문에 상태 변경에 의한 버그 발생 확률 감소 <br><br>

- 캐싱과 성능 최적화
  - 불변 객체는 변하지 않기 때문에 캐시하거나 재사용하기에 적합 <br><br>


### 6. 단점
- 불변 객체의 복잡성
    - 객체의 상태를 변경할 수 없기 때문에, 상태 변경이 필요한 경우 새로운 객체를 생성해야 하므로 <br>
      복잡한 객체를 다룰 때, 불변 객체 설계가 어려울 수 있음 <br><br>

- 메모리 사용량
    - 객체의 상태를 변경할 수 없기 때문에 새로운 상태를 가지는 객체를 만들 때마다 새로운 객체를 생성해야 하므로 <br>
      메모리 사용량 증가 -> 성능 이슈 발생  <br><br>
      
      
### 7. Immutable 예 1
```Java
String a = "WHO";
String b = "ARE YOU";
String c = a.concat(b); // ---> Immutable 패턴이 적용된 concat 메소드 실행으로 새로운 String 인스턴스가 생성되어 c에 인스턴스 참조 값이 저장. 
```
- 위 코드를 실행하면 concat 메소드 실행 시, 새로운 String 인스턴스가 생성된다.
- 즉, 기존의 String 변수 a나 b의 값이 변형되는 것이 아닌 새로운 String 인스턴스를 생성하고 값을 저장한다는 것이 핵심이다.<br/><br/>



### 8. Immutable 예 2
- 아래의 코드는 JDK의 String 클래스의 소스코드에 포함되어 있는 concat과 substring 메소드이다.
```Java
public String concat(String str) {
    int otherLen = str.length();
    if (otherLen == 0) {
        return this;
    }
    char buf[] = new char[count + otherLen];
    getChars(0, count, buf, 0);
    str.getChars(0, otherLen, buf, count);

    // 새롭게 만들어진 문자들이 할당된 String 인스턴스를 새로 생성하여 반환.
    return new String(0, count + otherLen, buf);  
}

public String substring(int beginIndex, int endIndex) {
    if (beginIndex < 0) {
        throw new StringIndexOutOfBoundsException(beginIndex);
    }
    if (endIndex > count) {
        throw new StringIndexOutOfBoundsException(endIndex);
    }
    if (beginIndex > endIndex) {
        throw new StringIndexOutOfBoundsException(endIndex - beginIndex);
    }

    // 새롭게 만들어진 문자들이 할당된 String 인스턴스를 새로 생성하여 반환.
    return ((beginIndex == 0) && (endIndex == count)) ? this : new String(offset + beginIndex, endIndex - beginIndex, value);   
}
```
- 코드에서 알 수 있듯이, 문자열을 변형시키는 2가지 메소드인 concat과 substring은 내부적으로 새로운 문자열 버퍼를 구성하고 스트링 인스턴스를 생성과 문자열 변형 작업을 수행한 뒤에 마지막 return 시, 반드시 새로운 String 인스턴스를 생성하고 변형된 문자열을 저장하여 반환한다. <br><br>
- 이러한 Immutable 패턴 방식은 멀티쓰레드 환경에서 발생하는 다양한 문제들(ex : 공용 인스턴스의 값 변화로 인해 다른 쓰레드에 원치않는 영향을 끼치는)을 원천적으로 해결할 수 있다.
