# 1장 사용자 수에 따른 규모 확장성

## 단일서버

1. 사용자는 도메인 이름을 이용하여 웹사이트에 접속. 이 접속을 위해서는 도메임 이름을 이름 서비스(DNS)에 질의하여 IP주소로 변환하는 과정이 필요함.
2. DNS 조회 결과로 IP 주소가 반환됨.
3. 해당 IP 주소로 HTTP 요청이 전달됨.
4. 요청을 받은 웹 서버는 HTML 페이지나 JSON 형태의 응답 반환

## 데이터베이스

사용자가 늘면 서버하나로는 충분하지 않아서 여려대를 둔다. 보통 하나는 웹/모바일 트래픽 처리 용도이고 다른 하나는 데이터 베이스 용

### 어떤 데이터베이스를 사용할 것인가? (NoSQL vs RDBMS)

- 비 관계형 데이터베이스를 선택해야되는 경우
1. 아주 낮은 응답 지연시간(latency)이 요구됨
2. 다루는 데이터가 비정형(unstructured)이라 관계형 데이터가 아님
3. 데이터(JSON, YAML, XML 등)를 직렬화하거나(serialize) 역직렬화(deserialize)할 수 있기만하면됨
4. 아주 많은 양의 데이터를 저장할 필요가 있음.


## 수평적 확장(Scale-Out) VS 수직적 확장(Scale-Up)

서버로 유입되는 양이 적은 경우에는 수직적 확장이 좋지만 몇가지 단점이 존재함
1. 수직적 규모 확장에는 한계가 있음. 한 대의 서버에 CPU나 메모리를 무한대로 증설할 방법은 없다.
2. 수직적 규모 확장법은 장애에 대한 자동복구(failOver)방안이나 다중화(re-dundancy)방안을 제시하지 않는다. 서버에 장애가 발생하는 웹사이트/앱은 완전히 중단됨

이런 단점 때문에 대규모 어플리케이션에서는 수평적 규모 확장법이 적절함

## 로드밸런서

부하 분산 집합에 속한 웹 서버들에게 트래픽 부하를 고르게 분산하는 역할을 한다. </br>
사용자는 로드밸런서의 공개 IP주소로 접속한다. 따라서 웹 서버는 클라이언트의 접속을 직접 처리하지 않는다.

- 서버 1이 다운되면 모든 트래픽은 서버 2로 전송된다. 따라서 웹 사이트 전체가 다운되는 일이 방지된다. 부하를 나누기 위해 새로운 서버를 추가할 수도있다.
- 웹사이트로 유입되는 트래픽이 가파르게 증가하면 두 대의 서버로 트래픽을 감당할 수 없는 시점이 오는데, 로드밸런서가 있으므로 우아하게 대처할 수 있다.
웹 서버 계층에 더 많은 서버를 추가하기만 하면 된다. 그러면 로드밸런스가 자동적으로 트래픽을 분산하기 시작할 것이다.

## 데이터베이스 다중화

많은 데이터베이스 관리 시스템이 다중화를 지원한다. 보통은 서버 사이에 주(Master)-부(Slave) 관계를 설정하고 데이터 원본은 주서버에 사본은 부 서버에 저장하는 방식입니다.
</br>
쓰기 연산은 마스터에서만 지원한다. 부 데이터베이스는 주 데이터베이스로부터 그 사본을 전달받으며, 읽기 연산만을 지원한다. 데이터베이스를 변경하는 명령어들, insert delete update등은 주 데이터베이스로만
전달되어야 한다.

## 캐시

캐시는 값비싼 연산 결과 또는 자주 참조되는 데이터를 메모리 안에 두고, 뒤이은 요청이 보다 빨리 처리될 수 있도록 하는 저장소이다. </br>
웹 페이지를 새로고침 할 때마다 표시할 데이터를 가져오기 위해 한 번 이상의 데이터베이스 호출이 발생한다. </br>
애플리케이션의 성능은 데이터베이스를 얼마나 자주 호출하느냐에 크게 좌우됨.

### 캐시 계층

캐시 계층(cache tier)은 데이터가 잠시 보관되는 곳으로 데이터베이스보다 훨씬 빠르다. 
별도의 캐시 계층을 두면 성능이 개선될 뿐 아니라 데이터베이스의 부하를 줄일 수 있고, </br>
캐시 계층의 규모를 독립적으로 확장시키는 것도 가능해진다. 

- 캐시 사용 시 주의할 점

1) 캐시는 어떤 상황에 바람직한가?
```
데이터 갱신은 자주 일어나지 않지만 참조는 빈번하게 일어난다면 고려해볼만 하다.
```

2) 어떤 데이터를 캐시에 두어야 하는가?
```
캐시는 데이터를 휘발성 메모리에 두므로, 영속적으로 보관할 데이터를 캐시에 두는 것은 바람직하지 않다.
예를 들어 캐시 서버가 재시작되면 캐시 내의 데이터는 모두 사라진다. 중요한 데이터는 영속성 데이터에 두어야한다.
```

3) 캐시에 보관된 데이터는 어떻게 만료(expire)되는가?
```
일관성은 데이터 저장소의 원본과 캐시 내의 사본이 같은지 여부다.
저장소의 원본을 갱신하는 연산과 캐시를 갱신하는 연산이 단일 트랜잭션으로 처리되지 않는 경우 이 일관성은 깨질 수 있다.
여러 지역에 걸쳐 시스템을 확장해 나가는 경우 캐시와 저장소 사이의 일관성을 유지하는 것은 어려운 문제가 된다.
```

4) 일관성(consistency)은 어떻게 유지되는가?
```
일관성은 데이터 저장소의 원본과 캐시 내의 사본이 같은지 여부다. 저장소의 원본을 갱신하는 연산과 캐시를 갱신하는 연산이
단일 트랜잭션으로 처리되지 않는 경우 이 일관성은 깨질 수 있다. 여러 지역에 걸쳐 시스템을 확장해 나가는 경우 캐시와 
저장소 사이의 일관성을 유지하는 것은 어려운 문제가 된다.
```

5) 장애는 어떻게 대처할 것인가?
```
캐시 서버를 한 대만 두는 경우 해당 서버는 단일 장애 지점(SPOF)이 되어버릴 가능성이 있다.

```
- 캐시 메모리는 얼마나 크게 잡을 것인가?
캐시 메모리가 너무 작으면 액세스 패턴에 따라서는 데이터가 너무 자주 캐시에서 밀려나버려 캐시의 성능이 떨어지게 됨.</br>
이를 막을 한가지 방법은 캐시 메모리를 과할당(overprovision)하는 것이다.

- 데이터 방출(eviction) 정책은 무엇인가?
캐시가 꽉 차버리면 추가로 캐시에 데이터를 넣어야 할 경우 기존 데이터를 내보내야 한다. 이것을 캐시 데이터 방출 정책이라 하는데, </br>
그 가운데 가장 널리 쓰이는 것은 LRU(Least Recently Used - 마지막으로 사용된 시점이 가장 오래된 데이터를 내보내는 정책)
다른 정책으로는 LFU(Least Frequently Used - 사용된 빈도가 가장 낮은 데이터를 내보내는 정책), </br>
FIFO(First In First Out - 가장 먼저 캐시에 들어온 데이터를 가장 먼저 내보내는 정책)

## CDN (콘텐츠 전송 네트워크)

CDN은 정적 콘텐츠를 전송하는데 쓰이는 지리적으로 분산된 서버의 네트워크이다. </br>
이미지, 비디오, CSS, JavaScript파일 등을 캐시할 수 있다.

- 비용 </br>
CDN은 보통 제 3사업자에 의해 운영되며, CDN으로 들어가고 나가는 데이터 전송 양에 따라 요금을 내게 된다. </br>
자주 사용되지 않는 콘텐츠를 캐싱하는 것은 이득이 크지 않으므로 CDN에서 빼는 것을 고려하도록 하자.

- 적절한 만료 시한 설정
- CDN 장애에 대한 대처 방안

