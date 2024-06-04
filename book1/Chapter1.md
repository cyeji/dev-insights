# 1. 잘못된 구조의 문제 깨닫기

## 1.1 의미를 알 수 없는 이름

- 기술 중심 명명
: 기술을 기반으로 이름을 붙이는 것
ex) 자료형 이름을 나타내는 Int, 메모리 제어를 나타내는 Memory, Flag

```JAVA
// 멤버십 관련 코드 관리
public class MembershipManager{ 
    // 
}
// MembershipManager, MembershipService, MembershipServiceImpl
// FeeAmtMap, LangCodeMap
```

- 일련번호 명명
: 클래스와 메서드에 번호를 붙여서 이름 짓는 것

```JAVA
class Class001{
    void method001();
    void method002();
    void method003();
    //...
}
```

## 1.2 이해하기 어렵게 만드는 조건 분기 중첩
- 코드의 중첩이 많을수록 코드의 가독성이 나빠집니다.

```JAVA
void test(){
    if(조건){
        //
        if(조건){
            //
        }
    }
}
```

-- 코드를 보고 무슨 이런 코드가 다 있나 싶기도 하지만 --

### 1.3 수많은 악마를 만들어 내는 데이터 클래스

데이터 클래스 : 데이터를 갖고 있기만 하는 클래스 

ex) 업무 계약을 다루는 서비스에서 계약 금액을 처리하는 요구 사항을 클래스로 구현

```java
public class ContractAmount{
	
	public int amountIncludingTax; // 세금 포함 금액
	public BigDecimal salesTaxRate; // 소비세율

}
```

다른 클래스에 계산 로직이 구현된 경우

```java
// 계약을 관리하는 클래스
public class ContractManager {
	public ContractAmount contractAmount;
	
	// 세금 포함 금액 계산
	public int calculateAmountIncludingTax(int amountTax, BigDecimal salesTaxRate){
		// ...
	}
	
	// 계약 체결
	public void conclude(){
		// ..
		
	}
}
```

### 1.3.1 사양을 변경할 때 송곳니를 드러내는 악마

소비세율을 변경하는 로직이 다방면으로 존재

중복되는 일을 하는 메소드가 있음을 말함

데이터와 로직 등이 분산되어 있는 것을 응집도가 낮은 구조라고 합니다. 응집도가 낮아서 생길 수 있는 문제

R1.3.2 코드의 중복

관련된 코드가 서로 멀리 떨어져있으면 관련된 것끼리 묶어서 파악하기 힘듭니다. 

1.3.3 수정 누락

코드의 중복이 많으면 사양이 변경될 때 중복된 코드를 모두 고쳐야됩니다.

1.3.4 가독성 저하

가독성이란 ? 코드의 의도나 처리 흐름을 얼마나 빠르고 정확하게 읽고 이해할 수 있는지 나타내는 지표입니다. 코드가 분산되어 있으면 중복된 코드를 포함해서 관련된 정보를 다 찾는 것 만으로도 시간이 오래 걸립니다. 따라서 가독성이 떨어지는 것입니다.

1.3.5 초기화되지 않은 상태 (쓰레기 객체)

추가로 초기화해야 하는 클래스라는 것을 모르면 발생하기 쉬운 불완전한 클래스

```java

ContractAmount amount = new ContractAmount();
System.out.println(amount.salesTaxRate.toString());
```

1.3.6 잘못된 값 할당

요구사항에 맞지 않는 값을 할당했을 떄

- 주문 건수가 음수가 나오는 경우
- 게임에서 히트포인트 값이 최대 값을 넘는 경우

```java
ContractAmount amount = new ContractAmount();
amount.salesTaxRate = new BigDecimal("-0.1");
```

## 1.4 악마 퇴치의 기본