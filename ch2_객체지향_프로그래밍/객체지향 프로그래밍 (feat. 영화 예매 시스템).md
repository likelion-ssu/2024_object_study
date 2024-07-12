<p style="text-align:end">
	2024-07-06 22:40 | 오브젝트
</p>

# 객체지향 프로그래밍을 향해

---

> 객체지향 프로그램을 작성할 때 가장 먼저 고려하는 것은 무엇인가?
> 대부분의 사람들은 클래스를 결정한 후에 클래스에 어떤 속성과 메서드가 필요한지 고민한다.
> ...
> 객체지향은 말 그대로 객체를 지향하는 것이다. 진정한 객체지향 패러다임으로의 전환은 <u>클래스가 아닌 객체</u>에 초점을 맞출 때에만 얻을 수 있다.

객체지향 프로그래밍은 다음의 두 가지에 집중해야 한다.

1. <strong style="color:#7a7a7a">어떤 클래스가 필요한지를 고민하기 전에, </strong>**어떤 객체들이 필요한지 고민**
	- 클래스는 공통적인 상태와 행동을 공유하는 객체들을 추상화한 것!
	- 따라서 어떤 객체들이 어떤 상태와 행동을 가지는지를 먼저 결정

2. <strong style="color:#7a7a7a">객체를 독립적인 존재가 아니라</strong>, **기능을 구현하기 위한 협력하는 공동체의 일원으로 바라보기**
	- 객체는 다른 객체에게 도움을 주거나 의존하는 협력적 존재
	- 상세 내용은 3장 참조

공통된 특성과 상태를 가진 객체들을 타입으로 분류! 이 타입을 기반으로 클래스를 구현!!

## 요구사항 🏷️

영화 예매 시스템 개발

- **영화(movie)**
	- '제목', '상영시간', '가격 정보' 등 영화에 대한 기본 정보를 포함
- **상영(screening)**
	- 실제로 관객들이 영화를 관람하는 사건
	- '상영 일자', '시간', '순번' 등을 포함
- **할인 정책(discount policy)**
	- 할인 요금을 결정
	- 영화 별로 최대 하나의 할인 정책만 할당 가능
	- **금액 할인 정책(amount discount policy)**: 예매 요금에서 일정 금액 할인
	- **비율 할인 정책(percent discount policy)**: 정가에서 일정 비율의 요금을 할인
- **할인 조건(discount condition)**
	- 조건에 따라 가격의 할인 여부를 결정
	- 하나의 할인 정책에 다수의 할인 조건을 혼용하여 지정 가능
	- **순서 조건(sequence condition)**: '상영 순번'이 일치하면 할인
	- **기간 조건(period condition)**: '요일', '시작 시간', '종료 시간'으로 구성. 영화 상영 시작 시간이 해당 기간에 포함되면 할인
+ **예매(reservation)**
	+ 제목, 상영정보, 인원, 정가, 결제금액 등 예매 정보를 포함

![[표_2.1.png|600]]

## 도메인 모델과 클래스 다이어그램 🏷️

객체지향 개발 단계 중에는 분석과 설계가 존재한다.

1. <span style="color:#a9a9a9">계획</span>
2. **분석 (Object-Oriented Analysis, OOA)**
	- 도메인 개념 또는 실세계 객체를 시각적으로 표현하는 단계
3. **설계 (Object-Oriented Design, OOD)**
	- 구현에 필요한 내역을 설계 모형으로 제작하고 상세화하는 단계
4. <span style="color:#a9a9a9">구현 (Object-Oriented Programming, OOP)</span>
5. <span style="color:#a9a9a9">테스트 및 검증</span>

이 중에서 분석에는 **도메인 모델**(Domain Model / Conceptual Class Diagram)이, 설계에는 **클래스 다이어그램**(Class Diagram)이 사용될 수 있다.
둘 다 클래스 다이어그램의 표기를 차용하지만, 사용되는 개발 단계가 다르기 때문에 다음과 같은 차이점이 존재한다.

|                     도메인 모델                      |                   클래스 다이어그램                    |
| :---------------------------------------------: | :--------------------------------------------: |
| 개념적인(conceptual) 관점에서 시스템을 이루는 도메인 객체 간의 관계를 표현 | 소프트웨어(software) 관점에서 시스템 내부의 클래스와 관계를 세부적으로 표현 |

![[Domain_Model v.s. Design_Model.png|500]]

### 도메인 모델

- **도메인**: 
	- 문제를 해결하기 위해 사용자가 프로그램을 사용하는 분야
	- 우리가 해결하려는 분야는 영화 예매 도메인

![[그림_2.3.png]]

![[그림_2.4.png]]

### 클래스 다이어그램



# 캡슐화와 접근 제어

---

+ **캡슐화(encapsulation)**: 데이터(상태)와 기능(행동)을 객체 내부로 함께 묶는 것
+ **접근 제어(access control)**: 외부에서 내부로의 접근을 통제하는 것

이러한 캡슐화와 접근제어는 객체의 외부와 내부의 경계를 명확하게 하고, 이를 통해 객체의 자율성을 보장한다. 외부에서는 객체의 상태를 알지 못하고, 행동에 간섭하지 못한다.

이 외부와 내부에서 접근 가능한 부분을 각각 ==**퍼블릭 인터페이스(public interface)**==와 ==**구현(implementation)**==이라고 한다. 객체 외부에서는 구현에 접근할 수 없고, 오직 퍼블릭 인터페이스를 통해 메시지를 보내는 것만 할 수 있다. 메시지로 인해 어떤 메서드가 수행되는지 또한 알지 못한다.

퍼블릭 인터페이스는 접근 수정자(access modifier) 중 `public`으로 지정된 메서드만을 포함하고,
구현은 `private` 메서드나 `protected` 메서드, 속성을 포함한다.

###### Screening 클래스 코드
```java
public class Screening {
	private Movie movie;    // 구현
	private int sequence;
	private LocalDateTime whenScreened;

	...

	// 구현
	private Money calculateFee(int audienceCount) {
		// movie 객체의 퍼블릭 인터페이스를 통해 메시지를 보냄
		// 하지만 내부적으로 어떤 행동을 하는지 알 수 없고, 간섭할 수도 없음
		return movie.calculateMovieFee(this).times(audienceCount);
	}

	// 퍼블릭 인터페이스
	public Reservation reserve(Customer customer, int audienceCount) {
		return new Reservation(customer, this, 
			calculateFee(audienceCount), audienceCount);
	}
}
```

## 구현 은닉 🏷️

- **구현 은닉(implementation hiding)**
	클라이언트 프로그래머에게 ==필요한 부분만 공개==하고, ==숨겨놓은 나머지 부분에 마음대로 접근할 수 없도록 방지==하는 것

+ **구현 은닉 장점**
	- **클라이언트 프로그래머**: 내부 구현은 무시한 채 인터페이스만 알아도 클래스 사용 가능
	- **클래스 작성자**: 인터페이스를 바꾸지 않는 한 외부에 미치는 영향을 걱정하지 않고도 내부 구현을 마음대로 변경 가능

![[#Screening 클래스 코드]]

`Screening` 클래스는 `movie` 객체가 어떻게 영화 요금을 계산하는지 알 수 없고, 알 필요도 없다. 단순히 퍼블릭 인터페이스를 통해 메시지를 보내고, 자기 할 일만 잘하면 된다.

`Movie` 클래스도 자신의 구현을 숨김으로써 자율성을 보장받는다. 영화 요금을 계산하는 방식이 달라져도 `calculateMovieFee` 메서드 내부만 수정하면 된다. 외부의 다른 객체가 수정에 받는 영향을 신경쓰지 않아도 된다.

# 객체 간의 협력

---

+ **협력(Collaboration)**: 
	+ ==시스템의 어떤 기능을 구현하기 위해 객체들 사이에 이뤄지는 상호작용==
	+ 객체지향 프로그램을 작성할 때는,
		1. 먼저 협력의 관점에서 어떤 객체가 필요한지를 결정하고
		2. 객체들의 공통 상태와 행위를 구현하기 위해 클래스를 작성한다.

영화를 예매하기 위해 `Screening`, `Movie`, `Reservation` 인스턴스들은 서로의 메서드를 호출하며 상호작용한다.

> [[#Screening 클래스 코드]]

![[그림_2.5.png]]

## 메시지와 메서드 🏷️

+ ==**메시지(message)**:==
	- 객체들이 협력(상호작용)하기 위한 유일한 의사소통 수단
	- **메시지 전송**: 객체가 다른 객체의 인터페이스에 공개된 행동을 수행하도록 요청하는 것
	- **메시지 수신**: 다른 객체의 요청이 도착하는 것
+ ==**메서드(method)**:==
	- 메시지를 수신한 객체가, 수신한 메시지를 처리하는 자신만의 방법

메시지와 메서드를 구분하는 것은 매우 중요하다. 메시지와 메서드의 구분에서부터 [[#다형성과 인터페이스 🏷️|다형성(polymorphism)]]의 개념이 출발한다.

###### Movie 클래스 코드
```java
public class Movie {
	private String title;
	private Duration runningTime;
	private Money fee;
	private DiscountPolicy discountPolicy;

	...

	public Money calculateMovieFee(Screening screening) {
		return fee.minus(discountPolicy.calculateDiscountAmount(screening));
	}
}
```

^movie-class

위는 `Movie` 클래스의 코드 중 일부이다.

**`Movie` 클래스는 `DiscountPolicy` 클래스에게 `calculateDiscountAmount` '메시지를 전송한다'.**
실은 `Movie` 클래스는 `DiscountPolicy` 안에 `calculateDiscountAmount` 메서드가 존재하는지조차 알지 못한다. `DiscountPolicy` 가 `calculateDiscountAmount` 메시지에 응답할 수 있다고 믿고 메시지를 전송할 뿐이다.

###### DiscountPolicy 추상 클래스 코드
```java
public abstract class DiscountPolicy {
	private List<DiscountCondition> conditions = new ArrayList<>();

	public Money calculateDiscountAmount(Screening screening) {
		// 전체 할인 조건에 대해 차례대로 screening이 할인 조건을 만족하는지 검사
		for (DiscountCondition each : conditions) {
			if (each.isSatisfiedBy(screening)) {
				// 추상 메서드 호출
				return getDiscountAmount(screening);
			}
		}
		// 만족하는 할인 조건이 하나도 존재하지 않는다면, 할인 요금은 0원
		return Money.ZERO;
	}

	// DiscountPolicy를 상속받은 자식 클래스의 오버라이딩 필요
	abstract protected Money getDiscountAmount(Screening screening);
}
```

^discountPolicy-class

**`DiscountPolicy` 클래스는 `calculateDiscountAmount` 메서드를 통해 이를 처리한다.**
해당 메서드는 할인 여부와 요금 계산에 필요한 전체적인 '흐름'은 정의하지만, 실제로 요금을 계산하는 부분은 추상 메서드인 `getDiscountAmount` 메서드에게 '위임'한다. ([[TEMPLATE METHOD 패턴|TEMPLATE METHOD 패턴]])

**`DiscountPolicy` 클래스는 다시 `DiscountCondition` 에게 `isSatisfiedBy` '메시지를 전송한다'.**

###### DiscountCondition 자바 인터페이스 코드
```java
public interface DiscountCondition {
	boolean isSatisfiedBy(Screening screening);
}
```

`DiscountCondition` 은 수신한 `isSatisfiedBy` 메시지를 처리하기 적절한 '메서드'를 스스로 선택한다. **객체의 타입에 따라, `SequenceCondition` 의 `isSatisfiedBy` 메서드나 `PeriodCondition` 의 `isSatisfiedBy` 메서드를 선택한다.**

전송자(`DiscountPolicy`)는 수신자(`DiscountCondition`)가 어떤 클래스의 인스턴스인지, 어떤 메서드를 호출하여 처리했는지 알 필요가 없다. 어떻게(How) 수행하는지는 모르고, 무엇을(What) 하는지만 알 뿐이다.

###### SequenceCondition 자바 구현체 코드
```java
public class SequenceCondition implements DiscountCondition {
	private int sequence;
	...
	public boolean isSatisfiedBy(Screening screening) {
		// 순번(sequence)과 파라미터로 전달된 Screening의 상영 순번이 일치하면 할인 가능
		return screening.isSequence(sequence);
	}
}
```
###### PeriodCondition 자바 구현체 코드
```java
public class PeriodCondition implements DiscountCondition {
	private DayOfWeek dayOfWeek;
	private LocalTime startTime;
	private LocalTime endTime;
	...
	public boolean isSatisfiedBy(Screening screening) {
		// 인자로 전달된 Screening의 상영 요일이 dayOfWeek과 같고,
		// 상영 시작 시간이 startTime과 endTime 사이에 있을 경우 할인 가능
		return screening.getStartTime().getDayOfWeek().equals(dayOfWeek) &&
			startTime.compareTo(screening.getStartTime().toLocalTime()) <= 0 &&
			endTime.compareTo(screening.getStartTime().toLocalTime()) >= 0;
	}
}
```

# 상속과 다형성

---

## 컴파일 시간 의존성과 실행 시간 의존성 🏷️

==**코드의 의존성(`컴파일 시간 의존성`)과 실행 시점의 의존성이 서로 다를 수 있다.**==

![[그림_2.7.png]]

위 그림은 `Movie` 와 `DiscountPolicy` 계층을 클래스 다이어그램으로 나타낸 것이다.

`Movie` 는 할인 정책이 금액 할인 정책인지, 비율 할인 정책인지를 판단하지 않는다.
할인 정책에 따른 영화 요금을 계산하기 위해서는 `AmountDiscountPolicy` 나 `PercentDiscountPolicy` 의 인스턴스에 의존해야 하지만, **코드 수준에서는 추상 클래스인 `DiscountPolicy`에만 의존한다.** ([[#Movie 클래스 코드|Movie 클래스 참조]])

```java
Movie avatar = new Movie("아바타",
						Duration.ofMinutes(120),
						Money.wons(10000),
						new AmountDiscountPolicy(Money.wons(800), ...));
```
^Movie-AmountDiscountPolicy

![[그림_2.8.png]]

그러나 **실행 시점에서 `Movie` 인스턴스는 `AmountDiscountPolicy` 나 `PercentDiscountPolicy` 의 인스턴스에 의존한다.**

### 설계의 트레이드오프

코드의 의존성과 실행 시점의 의존성이 다르면 다를수록, 설계가 유연해진다.

 하지만 ==**무조건 유연한 설계도, 무조건 읽기 쉬운 코드도 정답이 아니다.**==

1. **유연한 설계 → 코드를 이해하고 디버깅하기 어려움**
	- `Movie` 인스턴스가 의존하는 객체를 찾기 위해서는, 해당 인스턴스의 생성자에 전달되는 객체의 타입을 확인해야 함. ([[#^Movie-AmountDiscountPolicy|Movie 인스턴스 생성 참조]])
2. **유연성 억제 → 재사용성과 확장 가능성이 낮아짐**

## 상속과 인터페이스 🏷️

+ **상속(inheritance)**
	- 두 클래스 사이의 관계를 정의하는 방법
	- 기존 클래스가 가지고 있는 모든 속성과 행동을 새로운 클래스에 포함시킬 수 있다.

+ **차이에 의한 프로그래밍(programming by difference)**
	+ 부모 클래스와 다른 부분만을 추가해서 새로운 클래스를 쉽고 빠르게 만드는 방법
	+ e.g. `DiscountPolicy` 의 추상 메서드 `getDiscountAmount` 오버라이딩
		⇒ `DiscountPolicy` 의 행동을 수정한 `AmountDiscountPolicy` 와 `PercentDiscountPolicy` 추가

상속의 가치는 ==부모 클래스가 제공하는 **모든 인터페이스**를 자식 클래스가 물려받는 것==에 있다.
(메서드나 인스턴스 변수를 재사용하는 것이라는 일반적인 인식과 다르게)

상속을 통해 자식 클래스는 부모 클래스의 인터페이스를 포함하게 되어, 부모 클래스가 수신할 수 있는 모든 메시지를 수신할 수 있다. 
이 때문에 외부 객체는 자식 클래스를 부모 클래스와 동일한 타입으로 간주할 수 있다.

![[#^movie-class]]

`Movie` 가 `DiscountPolicy` 의 인터페이스에 정의된 `calculateDiscountAmount` 메시지를 전송하고 있다. `DiscountPolicy` 를 상속받는 `AmountDiscountPolicy` 와 `PercentDiscountPolicy` 의 인터페이스에도 이 오퍼레이션이 포함되어 있기 때문에, `DiscountPolicy` 대신에 `Movie` 와 협력할 수 있다.

+ **업캐스팅(upcasting)**:
	+ 자식 클래스가 부모 클래스를 대신하는 것
	+ e.g. `Movie` 생성자의 인자의 타입이 `DiscountPolicy` 임에도 `AmountDiscountPolicy` 와 `PercentDiscountPolicy` 의 인스턴스를 전달하는 것
	![[#^Movie-AmountDiscountPolicy]]

## 다형성과 인터페이스 🏷️

+ **다형성(polymorphism)**:
	+ 동일한 메시지를 수신했을 때 객체의 타입에 따라 다르게 응답할 수 있는 능력
	+ 객체가 동일한 **메시지**를 전송하여도, 실제로 어떤 **메서드**가 실행되는지는 ==**메시지를 수신하는 객체의 클래스**가 무엇이냐==에 따라 달라짐
	+ 다형적인 협력에 참여하는 객체들은 모두 **상속**을 통해 **인터페이스가 동일**해야 한다.

![[#^movie-class]]

|          컴파일 시간 의존성          |              실행 시간 의존성              |
| :--------------------------: | :---------------------------------: |
| `Movie` `→` `DiscountPolicy` | `Movie` `→` `AmountDiscountPolicy`  |
|                              | `Movie` `→` `PercentDiscountPolicy` |
다형성은 컴파일 시간 의존성(`DiscountPolicy`)과 실행 시간 의존성(`AmountDiscountPolicy`/`PercentDiscountPolicy`)을 다르게 만들 수 있는 객체지향의 특성을 이용해 서로 다른 메서드(`calculateDiscountAmount`)를 실행할 수 있게 한다.

+ **지연 바인딩(lazy binding):**
	+ 동적 바인딩(dynamic binding)
	+ 메시지와 메서드를 실행 시점에 바인딩하는 것
	+ 객체 지향이 하나의 메시지를 선택적으로 서로 다른 메서드에 연결할 수 있는 이유
	+ e.g. 실행 시점에 어느 클래스(`AmountDiscountPolicy`/`PercentDiscountPolicy`)의 `calculateDiscountAmount` 메서드를 실행할지 결정

+ 초기 바인딩(early binding):
	+ 정적 바인딩(static binding)
	+ 컴파일 시점에 실행될 함수나 프로시저를 결정하는 것
	+ 전통적인 함수 호출 방식

> [!NOTE] 구현 상속과 인터페이스 상속
> + **구현 상속(implementation inheritance)**
> 	+ 순수하게 코드를 재사용하기 위한 목적으로 상속을 사용하는 것
> 	+ 서브클래싱(subclassing)
> + **인터페이스 상속(interface inheritance)**
> 	+ 다형적인 협력을 위해 부모 클래스와 자식 클래스가 인터페이스를 공유할 수 있도록 상속을 이용하는 것
> 	+ 서브타이핑(subtyping)
> ---
> **상속은 구현 상속이 아니라 인터페이스 상속을 위해 사용해야 한다.**
> 구현을 재사용할 목적으로 상속을 사용하면 변경에 취약한 코드를 낳게 될 확률이 높다.
> 코드 재사용에는 구현 상속 대신에 [[#코드 재사용 🏷️|합성(composition)]]을 사용한다.

# 추상화와 유연성

---

+ **추상화(abstraction)**:
	+ **퍼블릭 인터페이스에 초점**을 맞추고, 구현의 일부(`추상 클래스`) 혹은 전체(`자바 인터페이스`)를 ==자식 클래스가 결정할 수 있도록 **결정권을 위임**==
	+ e.g. 할인 정책(`DiscountPolicy`) - 금액 할인 정책과 비율 할인 정책
	+ e.g. 할인 조건(`DiscountCondition`) - 순번 조건과 기간 조건

## 추상화의 장점 🏷️

1. 추상화의 계층만 따로 떼어 놓고 살펴보면 **[[#추상화 장점 1 - 상위 정책 기술의 편이성|요구사항의 정책을 높은 수준에서 서술 가능]]**
2. 추상화를 이용하면 **[[#추상화 장점 2 - 설계의 유연성|설계가 유연해짐]]**

![[그림_2.13.png]]

### 추상화 장점 #1 - 상위 정책 기술의 편이성

> **추상화를 사용하면 세부적인 내용을 무시한 채 ==상위 정책을 쉽고 간단하게 표현==할 수 있다.**

```
1. "영화 예매 요금은 최대 하나의 '할인 정책'과 다수의 '할인 조건'을 이용해 계산할 수 있다."

2. "영화의 예매 요금은 '금액 할인 정책'과 '두 개의 순서 조건, 한 개의 기간 조건'을 이용해서 계산할 수 있다."
```

1번 문장은 2번 문장을 포괄한다.
추상화는 세부사항에 억눌리지 않고 상위 개념만으로 도메인의 중요한 개념을 설명 가능하게 한다.

> **추상화를 이용해 상위 정책을 기술한다는 것은 기본적인 애플리케이션의 협력 흐름을 기술한다는 것을 의미한다.**

+ 영화의 예매 가격을 계산하기 위한 흐름은
	+ 항상 `Movie` `→` `DiscountPolicy` `→` `DiscountCondition` 으로 흐른다.
	+ 자식 클래스들은 추상화를 통해 정의한 상위의 협력 흐름을 그대로 따르게 된다.

### 추상화 장점 #2 - 설계의 유연성

> **추상화를 이용해 상위 정책을 표현하면 기존 구조를 수정하지 않고도 새로운 기능을 쉽게 추가하고 확장할 수 있다.**

+ 추상화를 중심으로 코드의 구조를 설계하면 ==**유연하고 확장 가능한 설계**==를 만들 수 있음
+ 이는 추상화가 ==**설계가 구체적인 상황에 결합되는 것을 방지**==하기 때문
	+ e.g. `Movie` 클래스는 특정할 할인 정책에 묶이지 않기 때문에, `DiscountPolicy` 를 상속받는 어떠한 할인 정책과도 협력이 가능

## 유연성, 그리고 추상화 🏷️

> **유연성이 필요한 곳에 추상화를 사용하라.**

<u>할인 정책이 적용되지 않는 영화</u>는, 할인 요금을 계산할 필요 없이 영화에 설정된 기본 요금을 그대로 사용해야 한다.

### 방식 #1

```java
public class Movie {
	public Money calculateMovieFee(Screening screening) {
		// 할인 정책이 없는 경우, 기본 요금을 그대로 사용
		if (discountPolicy == null) {
			return fee;
		}
		
		return fee.minus(discountPolicy.calculateDiscountAmount(screening));
	}
}
```

협력의 설계 측면에서, 책임의 위치를 결정하기 위해 **조건문**을 사용하는 것은 대부분 좋지 않은 선택이다. 지금까지 **일관성 있던 협력 방식이 무너지기 때문**이다.

|        -         |         할인 금액 계산하는 책임         |                              위치                              |
| :--------------: | :---------------------------: | :----------------------------------------------------------: |
|   **기존 할인 정책**   | `DiscountPolicy` 의 <br>자식 클래스 | `DiscountPolicy` 의 자식 클래스의 <br>`calculateDiscountAmount` 메서드 |
| **할인 정책이 없는 경우** |          `Movie` 클래스          |     `if (discountPolicy == null)` <br>`{ return fee; }`      |

⇒ ==**예외 케이스 최소화 & 일관성 유지가 중요!!**==

### 방식 #2

```java
public class NoneDiscountPolicy extends DiscountPolicy {
	@Override
	protected Money getDiscountAmount(Screening screening) {
		return Money.ZERO;
	}
}
```

```java
Movie starWars = new Movie("스타워즈",
						  Duration.ofMinutes(210),
						  Money.wons(10000),
						  new NoneDiscountPolicy());
```

`NoneDiscountPolicy` 클래스를 추가하여 일관성을 유지하였다. 
(할인 요금을 계산할 책임을 `DiscountPolicy` 계층에 유지)

==**애플리케이션의 기능을 확장하는 데에, 기존 코드(`Movie&DiscountPolicy`)가 수정되지 않음**==

⇒ `NoneDiscountPolicy` 클래스가 상속받는 `DiscountPolicy` 가 추상적이기 때문에 가능
⇒ 추상화는 유연하고 확장 가능한 설계를 가능하게 한다.

![[그림_2.14.png]]

## 추상 클래스와 인터페이스 트레이드오프 🏷️

> 구현과 관련된 모든 것들이 트레이드오프의 대상이 될 수 있다.

### 방식 #3

`NoneDiscountPolicy` 클래스의 `getDiscountAmount` 메서드는 사실 반환 값이 무엇이든 상관 없다.

![[#^discountPolicy-class]]

부모 클래스 `DiscountPolicy` 는 할인 조건을 차례대로 검사하며, 만족하는 할인 조건이 있는 경우에 `getDiscountAmount` 메서드를 호출한다.
하지만, `NoneDiscountPolicy` 는 **할인 조건이 없기 때문**에 `getDiscountAmount` 메서드가 호출되지 않기 때문이다.

부모 클래스 `DiscountPolicy` 의 `calculateDiscountAmount` 메서드에서, 할인 조건이 없는 경우 0원을 반환하는 것을 바탕으로 `NoneDiscountPolicy` 가 설계되었다.
이는 **`DiscountPolicy` 와 `NoneDiscountPolicy` 를 개념적으로 결합**시킨다.

```java
public interface DiscountPolicy {
	Money calculate DiscountAmount(Screening screening);
}
```

```java
public abstact class DefaultDiscountPolicy implements DiscountPolicy {
	...
}
```

```java
public class NoneDiscountPolicy implements DiscountPolicy {
	@Override
	public Money calculateDiscountAmount(Screening screening) {
		return Money.ZERO;
	}
}
```

이 문제를 해결하기 위해
1. `DiscountPolicy` 클래스를 자바 인터페이스로 변경
2. 기존 `DiscountPolicy` 클래스의 이름을 `DefaultDiscountPolicy`로 변경. 자바 인터페이스를 구현하도록 수정
3. `NoneDiscountPolicy`가 자바 인터페이스를 구현하도록 수정

![[그림_2.15.png]]

+ **트레이드오프**
	+ 이상:
		+ 인터페이스를 사용하도록 변경한 설계가 더 좋음
	+ 현실:
		+ `NoneDiscountPolicy` 만을 위해 인터페이스를 추가하는 것이 과함
		+ 변경 전의 코드(방식 #2)도 할인 금액이 0원이라는 사실을 효과적으로 전달

⇒ 구현에 관련된 모든 것들이 트레이드오프의 대상이 될 수 있다.
⇒ 고민하고 트레이드오프해라.

## 코드 재사용 🏷️



### 상속을 통한 코드 재사용



### 합성




# Hands-on 🤲

---

일전에 프로젝트를 수행하던 중에 난항을 겪은 적이 있다.

당시에 클라이언트의 요청에 따라 게시글 데이터들을 응답하는 API를 제작하였다.
해당 API는 오름차순, 내림차순 등 여러 조건에 따라 다양한 메서드를 실행하고, 각 메서드들은 모두 `List<ArticleResponse>` 객체를 클라이언트에게 응답으로 보낸다.

`ArticleResponse` 클래스는 아래와 같이 구현하였다.

```java
@Builder
public class ArticleResponse {

	private String title;

	private int content;

	private LocalDateTime createdAt;

	public ArticleResponse(String title, int content, LocalDateTime createdAt) {
		this.title = title;
		this.content = content;
		this.createdAt = createdAt;
	}

	public static ArticleResponse create(Article article) {
		return ArticleResponse.builder()
				.title(article.getTitle())
				.content(article.getContent())
				.createdAt(article.getCreatedAt());
	}

	public String getTitle() { return this.title; }
	public String getContent() { return this.content; }
	public String getCreatedAt() { return this.createdAt; }
}
```

- `@Builder`
	- `ArticleResponse` 클래스에 `builder()` 라는 메서드를 추가하는 어노테이션이다.
	- `builder` 메서드
		- `ArticleResponse` 인스턴스를 생성하는 데에 사용된다.
		- `ArticleResponse` 클래스의 특정 속성(변수)만을 포함시켜서 인스턴스를 생성할 수도 있다.
	- `@Builder` 어노테이션이 적용되기 위해서, 해당하는 클래스는 모든 속성을 포함하는 생성자 `ArticleResponse(String title, int content, LocalDateTime createdAt)` 를 반드시 구현해야 한다.
+ `create` 메서드
	+ 정적 팩토리 생성자 메서드이다.
	+ 클래스 외부에서 생성자 대신 `create` 메서드를 사용하여 인스턴스를 생성할 수 있다.
	+ 내부적으로 `builder` 메서드를 사용함으로써 코드의 가독성을 높이고자 했다.

해당 응답 클래스를 사용하는 API를 팀원들과 함께 협업하여 구현하였고, 모든 상황에서 올바르게 동작하였다.
하지만 해당 클래스에는 심각한 문제가 존재한다. 무엇일까?

# What you need to know ✅

----

#캡슐화 #접근제어 #구현은닉 #상속 #다형성 #추상화 #유연성 #메시지 #메서드 #퍼블릭인터페이스 #구현 #동적바인딩 