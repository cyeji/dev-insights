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

## 1.3 수많은 악마를 만들어내는 데이터 클래스

## 1.4 악마 퇴치의 기본