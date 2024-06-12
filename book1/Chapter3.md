## 3.1 클래스 단위로 잘 동작하도록 설계하기

‘클래스 단위로도 잘 동작하게 설계해야 한다’

클래스는 클래스 하나로도 잘 동작할 수 있도록 설계해야 합니다. 또한 복잡한 초기 설정을 하지 않아도 곧바로 사용할 수 있게 만들어야 합니다. 그리고 클래스를 마음대로 조작해서 클래스 전체가 고장나는 일(버그 발생)이 없게, 최소한의 조작 방법(메서드)만 외부에 제공해야 합니다.

### 3.1.1 클래스의 구성 요소

- 인스턴스 변수
- 인스턴스 변수에 잘못된 값이 할당되지 않게 막고, 정상적으로 조작하는 메서드

### 3.1.2 모든 클래스가 갖추어야 하는 자기 방어 임무

‘따로 초기화하지 않거나 사전 준비를 하지 않으면 사용할 수 없는’ 클래스와 메서드는 사용하고 싶지 않을 것이다.

자신의 몸은 자신이 지켜야하는 자기 방어 임무 수행이 필요

이를 위해서는 데이터 클래스에 자기 방어 임무를 부여해서 다른 클래스에 맡기던 일을 스스로 할 수 있게 설계하면 됩니다.

## 3.2 성숙한 클래스로 성장시키는 설계 기법

- 금액을 나타내는 클래스

```java
public Money{
	int amount;
	Currency currency;
}
```

### 3.2.1 생성자로 확실하게 정상적인 값 설정하기

데이터 클래스는 디폴트 생성자(매개변수 없는 생성자)를 사용해서 인스턴스를 생성한 뒤 인스턴스 변수에 따로 값을 할당해서 초기화합니다. ‘로우 데이터 객체’로서 초기화되지 않은 상태를 유발하는 클래스 구조입니다.

로우 데이터 객체 방지를 위해서는 클래스 인스턴스를 생성하는 시점에 확실하게 인스턴스 변수가 정상적인 값을 만들도록 함. 적절한 초기화 로직을 생성자에 구현

- 생성자에서 초기화하기

```java
class Money{
	int amount;
	Currency currency;
	
	Money(int amount, Currency currency){
		this.amount = amount;
		this.currency = currency;
	}
}
```

매개변수에 값이 잘못된 상태로 프로그램이 동작하면 버그가 발생합니다. 잘못된 값이 유입되지 못하게 유효성 검사를 생성자 내부에 정의합니다. 잘못된 값이라면 곧바로 예외를 발생시키도록 구현합니다.

- 금액 amount : 0 이상의 정수
- 통화 currency : null 이외의 것

```java
class Money{
	int amount;
	Currency currency;
	
	Money(int amount, Currency currency){
		if(amount < 0){
			throw new IllegalArgumentException("금액은 0 이상의 값을 지정해주세요.");	
		}
		if(currency == null){
			throw new NullPointerException("통화 단위를 지정해 주세요.");
		}
		this.amount = amount;
		this.currency = currency;
	}
}
```

### 3.2.2 계산 로직도 데이터를 가진 쪽에 구현하기

‘응집도가 낮은 구조’ ⇒ 데이터와 데이터를 조작하는 로직이 분리되어 있는 구조

```java
class Money{
	// 생략
	void add(int other){
		amount += other;
	}
}
```

### 3.2.3 불변 변수로 만들어서 예상하지 못한 동작 막기

변수의 값이 계속 바뀌면 값이 언제 변경되는지, 지금 값은 무엇인지 계속 신경써야합니다. 비즈니스 요구사항이 바뀌어서 코드를 수정하다가 의도하지 않은 값을 할당하는 ‘예상치 못한 부수 효과’가 발생할 수 있습니다.

```java
money.amount = originalPrice;

if(specialServiceAdded){
	money.add(additionalServiceFee);
	// 생략
	if(seasonOffApplied){
		money.amount = seasonPrice();
	}
}
```

이를 막기 위해서 인스턴스 변수를 불변(immutable)으로 만듭니다. 

final이 붙으면 한번만 할당할 수 있습니다.

```java
class Money{
	final int amount;
	final Currency currency;
	
	Money(int amount, Currency currency){
		//
	}
}
```

### 3.2.4 변경하고 싶다면 새로운 인스턴스 만들기

- 변경된 값을 가진 인스턴스 생성하기

```java
class Money{
	// 
	Money add(int other){
		int added = amount + other;
		return new Money(added, currency);
	}
}
```

### 3.2.5 메서드 매개변수와 지역변수도 불변으로 만들기

- 메서드 내부에서 매개변수를 변경

```java
void doSomething(int value){
	value = 100;
}
```

- final로 매개변수를 재할당하지 못하게 만들기

```java
void doSomething(final int value){
	value = 100; // 컴파일 오류
}
```

- add메서드의 매개변수도 final로 만들기

```java
class Money{
	
	Money add(final int other){
		int added = amount + other;
		return new Money(added, currency);
	}
}
```

- 지역변수에도 final을 붙여 불변으로 만들기

```java
class Money{
	
	Money add(final int other){
		final int added = amount + other;
		return new Money(added, currency);
	}
}
```

### 최종

```java
class Money{
	final int amount;
	final Currency currency;
	
	Money(final int amount, final Currency currency){
		if(amount < 0){
			throw new IllegalArgumentException("금액은 0 이상의 값을 지정해주세요.");
		}
		if(currency == null){
			throw new NullPointerException("통화 단위를 지정해주세요");
		}
		this.amount = amount;
		this.currency = currency;
	}
	
	Money add(final Money other){
		if(!currency.equals(other.currency)){
			throw new IllegalArgumentException("통화 단위가 다릅니다.");
		}
		
		final int added = amount + other.amount;
		return new Money(added, currency);	
	}
}
```