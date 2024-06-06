

# 2장 설계 첫걸음

## 2.1 의도를 분명히 전달할 수 있는 이름 설계하기

이름을 짧게 줄이면 입력해야하는 글자 수가 줄어듭니다. 하지만 이 코드를 다른 사람이 읽거나 시간이 지난 후 다시 볼 때는 읽고 이해하기 매우 어렵습니다.  

```java
int d = 0;
d = p1 + p2;
d = d - ((d1+d2) / 2);
if(d < 0) {
	d = 0;
}
```

이해하기 쉽지 않은 변수로 이름을 작성하면 후에 코드를 이해하는데 더 많은 시간이 걸릴 수 도 있습니다. 그러므로 의도를 알 수 있는 이름을 사용해서 개선해야합니다.

```java
int damageAmount = 0;
damageAmount = playerArmPower + playerWeaponPower;
damageAmount = damageAmount - ((ememyBodyDefence + enemyArmorDefence) / 2);

if(damageAmount<0){
	damageAmount = 0;
}
```

- 보험 나이 계산하는 메소드

ex) FrontUtil > getInsuranceAge


## 2.2 목적별로 변수를 따로 만들어 사용하기

대미지 크기를 나타내는 damageAmount에 값이 여러번 할당되고 있습니다. 재할당 같은 경우 변수의 용도가 바뀌는 문제를 일으키기 쉽습니다. 그러면 코드를 읽는 사람을 혼란 스럽게 만들고, 버그를 만들어 낼 가능성이 있습니다.

전체적으로 ‘어떤 값을 계산하는데 어떤 값을 사용하는지’ 관계를 파악할 수 있도록 개념이 다른 값에 변수를 만들어 할당하자

```java
int totalPlayerAttackPower = playerArmPower + playerWeaponPower;
int totalEnemyDefence = enemyBodyDefence + enemyArmorDefence;

int damageAmount = totalPlayerAttackPower + (totalEnemyDefence / 2);
if(damageAmount < 0){
	damageAmount = 0;
}
```

## 2.3 단순 나열이 아니라, 의미 있는 것을 모아 메소드로 만들기

일련의 흐름대로 코드를 작성하게 되면 어디에서 시작해서 어디에서 끝나는지 무슨 일을 하는지 알 수 없습니다.

이런 상황에 대비하기 위해서 의미 있는 로직을 모아서 메서드(함수)로 구현하는 것이 좋습니다.

공격력계산, 방어력 계산, 대미지 계산 로직 메서드 분리

```java
int sumUpPlayerAttackPower(int playerArmPower, int playerWeaponPower){
	return playerArmPower + playerWeaponPower;
}

int sumUpEnemyDefence(int enemyBodyDefence, int enemyArmorDefence){
	return enemyBodyDefence + enemyArmorDefence;
}

int estimateDamage(int totalPlayerAttackPower, int totalEnemyDefence){
	int damageAmount = totalPlayerAttackPower - (totalEnemyDefence / 2);
    return Math.max(damageAmount, 0);
}

```

세부 계산 로직을 메서드로 감싸므로 일련의 흐름이 쉽게 읽힙니다.

```java
int totalPlayerAttackPower = sumUpPlayerAttackPower(playerBodyPower, playerWeaponPower);
int totalEnemyDefence = sumUpEnemyDefence(enemyBodyDefence, enemyArmorDefence);
int damageAmount = estimateDamage(totalPlayerAttackPower, totalEnemyDefence);
```

ex) FrontRouteServiceImpl

## 2.4 관련된 데이터와 로직을 클래스로 모으기

클래스에서 데이터를 인스턴스 변수로 갖고, 인스턴스 변수를 조작하는 메서드를 모아놓는 곳

서로 밀접한 데이터와 로직을 한 곳에 모아두면, 이곳저곳 찾지 않아도 괜찮습니다.