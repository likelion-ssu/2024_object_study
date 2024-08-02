###  책임 주도 설계로의 전환

**데이터 중심 설계 -> 책임 중심 설계
1. 데이터보다 객체의 책임(행동)을 먼저 결정
	<span style="color:0000FF">a. 해당 객체가 수행해야 하는 책임은 무엇인가</span>
	<span style="color:0000FF">b. 해당 책임을 수행하는 데 필요한 데이터는 무엇인가</span>
2. 협력의 문맥 안에서 책임 결정
<span style="color:0000FF">책임의 품질은 협력에 적합한 정도로 결정 - 메시지 전송자에게 적합한 책임</span>

### 책임 할당을 위한 GRASP 패턴

> *GRASP(General Responsibility Assignment Software Pattern)패턴
> : 객체에게 책임을 할당할 때 지침으로 삼을 수 있는 원칙들의 집합을 패턴 형식으로 정리


**1. 도메인 개념

정확할 필요x -> 책임 할당 받을 객체의 종류 & 관계 표현 중심

![[a.png]]

**2. 정보 전문가에게 책임 할당

<span style="color:0000FF">a. 메시지를 전송할 객체는 무엇을 원하는가?</span>
<span style="color:0000FF">b. 메시지를 수신할 적합한 객체는 누구인가?</span>
-> 책임을 수행하는 데 필요한 * 정보를 가지고 있는 객체에게 할당 (INFORMATION EXPERT 패턴)

	정보= 해당 정보를 제공할 수 있는 다른 객체를 알고있음 or 해당 정보를 저장


**INFORMATION EXPERT 패턴

메시지 *예매하라* 의 처리 
-> 예매하는 데 필요한 정보 가장 많이 알고 있는 상영(Screening) 도메인

![[b.png]]
1. 예매 가격 계산 작업
	a. 예매에 필요한 정보 가장 많이 알고 있는 Screening 도메인을 정보 전문가로 지정 
	b. 예매 가격 = (영화 한 편 가격) x (인원수)
2. *영화 한 편 가격*
	a. Movie 도메인을 정보 전문가로 지정
	b. Movie의 작업- 요금 계산
	  할인 가능 여부 판단 -> 할인 정책 따른 할인 요금 제외 금액 계산
3. *할인이 가능한가*
	a. DiscountCondition을 정보 전문가로 지정
	b. 외부 메시지 요청 x
4. 할인 가능 여부 반환
5. 영화 한 편 가격 반환

=> INFORMATION EXPERT 패턴을 따름을 통해 자율성이 높은 객체들 간의 협력을 유도할 수 있음 


**3. 높은 응집도와 낮은 결합도 유지

![[c.png]]
-> 기능적 측면에서는 동일해 보이지만, 
- Movie-DiscountCondition 은 이미 결합 되어 있으니, Screening-DiscountCondition의 결합도 추가됨 -> 높은 결합도 
-  Screening-DC 협력 시 Screening의 책임 영역 모호해짐-> 낮은 응집도
-> 여러 협력 패턴 중 낮은 결합도(LOW COUPLING)와 높은 응집도(HIGH COHESION) 얻을 수 있는 설계를 선택 

**4. 창조자에게 객체 생성 책임 할당

	**CREATOR 패턴
		: 생성되는 객체와 높은 관련성을 지는 객체에 해당 객체를 생성할 책임을 맡기는 것.
		  단, 두 객체간의 결합을 염두에 두어야 한다.
		  아래의 조건 최대한 많이 만족하는 B에게 객체 생성 책임 할당 
		- B가 A객체를 포함하거나 참조
		- B가 A객체를 기록
		- B가 A객체를 긴밀하게 사용
		- B가 A객체를 초기화하는 데 필요한 데이터를 가짐 (이 경우, B는 A대한 정보 전문가)

 영화 예매 협력의 최종 결과물: Reservation 인스턴스 생성 
 -> Reservation과 긴밀한 연결을 가진 Screening을 CREATOR 로 선택 


###  ==+ GRASP 패턴==
-> 9가지의 원칙 
1. Controller: 컨트롤러는 모든 시스템 
2. Creator
3. Indirection
4. Information expert
5. High cohesion
6. Low coupling
7. Polymorphism
8. Protected variations
9. Pure fabrication

### 구현


Movie 클래스
```JAVA
public class Movie{
	privte Money calculateDiscountAmount(){
		switch(movieType){
			case AMOUNT_DISCOUNT:
				return calculateAmountDiscountAmount();
			case PERCENT_DISCOUNT:
				reurn calculatePercentDiscountAmount();
			case NONE_DISCOUNT:
				return calculateNoneDiscountAmount();
		}
	throw new IllegalStateException();
	}

	private Money calculateAmountDiscountAmount(){
		return discountAmount;
	}

	private Money calculatePercentDiscountAmount(){
		return fee.times(discoutnPercent);
	}

	private Money calculateNoneDiscountAmount(){
		return Money.ZERO;
	}
}
```

discountCondition 클래스
```JAVA
public class discountCondition{
	private DiscountConditionType type; //enum type
	private int sequence;
	private DatOfWeek dayOfWeek;
	private LocalTime startTime;
	private LocalTime endTime;
	
	public boolean isSatisfiedBy(Screening screening){
		if(type==DiscountConditionType.PERIOD){
			return isSatisfiedByPeriod(screening);
		}	
		return isSatisfiedBySequence(screening);
	}
	
	private boolean isSatisfiedByPeriod(Screening screening){
			return dayOfWeek.equals(screening.getWhenScreened().getDatOfWeek())&& startTime.compareTo(screening.getWhenScreened().toLocalTime())<=0 && endTime.isAfter(screening.getWhenScreened())>0;
	}

	private boolean isSatisfiedBySequence(Screening screening){
		return sequence==screening.getSequence();
	}
}
```

Screening 클래스
```JAVA
public class Screening{
	public LocalDateTime getWhenScreened(){
		return whenScreened;
	}
	public int getSequence(){
		return sequence;
	}
}
```

높은 변화의 가능성
- 새로운 할인 조건 추가
- 순번 조건 판단 로직 변경
- 기간 조건 판단 로직 변경 
### DiscountCondition 개선

 **문제 상황: 다소 연관성이 없는 기능/데이터들이 하나의 클래스 내에 존재하여 낮은 응집도
 -> 변경의 이유 따른 *클래스 분류* 필요 

==sol1) 순번 조건과 기간 조건 클래스 분리하여 구현==

장점: 개별 클래스의 응집도 향상
한계: 
	1. Movie-PeriodCondition, SequenceCondition 양쪽에 결합 -> 전체적 결합도 상승 
	2. 협력을 위한 목록 관리로 인해 수정 어려워짐 -> 캡슐화 성능 저하
	=> 변경과 캠슐화 관점에서의 설계 품질 저하 

==sol2) 다형성(POLYMORPHISM)을 통한 분리 ==

![[d.png]]

Movie입장에서 SequenceCondition과 PeriodCondition은 동일한 역할을 수행
-> 역할에 대해서만 결합되도록 의존성 제한 
	역할 대체할 클래스들 간의 구현 공유 o - 추상 클래스
	역할 대체할 클래스들 간의 구현 공유 x, 책임만 공유 -인터페이스 


인터페이스를 이용한 DiscountConditon 구현 
```
public interface DiscountCondition{
	boolean isSatisfiedBy(Screening screening);
}
```


```
public class Periodconditino implements DiscountCondition{...}

public clas SequenceConditino implements DiscountCondition{...} 
```

Movie 클래스
```
public class Movie{
	private List<DiscountCondition> discountConditions;

	public Money calculateMovieFee(Screentin screening){
		if(isDiscountable(screening)){
			return fee.minus(calculateDiscountAmount());
		}
		return fee;
	}

	private boolean isDiscountable(Screening screening){
		return discountConditions.stream().anyMatch(condition-> condition.isSatisfiedBy(screening))
	}

}
```

### Movie 클래스 개선

**문제 상황: 
1. 두 가지 타입(금액 할인 정책, 비율 할인 정책)을 하나의 클래스 안에 구현 -> 낮은 응집도
2. 새로운 할인 조건 추가 시 클래스의 수정 필요 -> 낮은 캡슐화 성능

==sol) PROTECTED VARIATIONS 패턴 ==

	PROTECTED VARIDATIONS 패턴:
	변화가 예상되는 불안정한 지점들을 식별하여 그 주위에 안정된 인터페이스 형성하도록 책임 
	할당하여, 변화와 불안정성으로 인한 다른 요소들의 피해 방지.
	**"설계에서 변화하는 것이 무엇인지 고려하고 변하는 개념을 캡슐화하라"**

![[e.png]]


POLTY MORPHISM 패턴을 사용한 PROTECRED VARIATION 패턴 달성
```JAVA
public abstract class Movie{ //구현 공유o -> 추상 클래스 
	privte String title;
	private Duration runningTime;
	private Money fee;
	private List<DiscountCondition> discountConditions;

	public Movie(String title, Duration runningTime, Money fee, DiscountCondition... discountConditions){
		this.title=title;
		this.runningTime=runningTime;
		this.fee=fee;
		this.discountConditions=Array.asList(discountConditions);
	}

	public Money calculateMovieFee(Screening screening){
		if(isDiscountable(screening)){
			return fee.minus(calculateDiscountAmount());
		}
		return fee;
	}

	private boolean isDiscountable(Screening screening){
		return discountConditions.stream().anyMatch(condition-> condition.isSatisfiedBy(screening));
	}

	abstract protected Money calculateDiscountAmount();
/*
* discountAmount, discountPercent 인스턴스 변수 삭제
* 해당 인스턴스 변수들 이용하는 메서드들 삭제
*/

}
```

AmountDiscountMovie 클래스 (- Movie 클래스의 서브클래스)

```
public class AmountDiscountMovie extends Movie{
	private Money discountAmount;

	public AmountDiscountMovie(String title, Duration runningTime, Money fee, Money discountAmount, DiscountCondition... discountConditions){
	super(title,runningTime,fee,discountConditions);
	this.discountAmount=discountAmount;	
	}
	
	@Override
	protected Money calculateDiscountAmount(){
		return discountAmount;
	}

}
```

PercentDiscountMovie클래스 (- Movie 클래스의 서브클래스)
```
public class PercentDiscountMovie extends Movie{
	private double percent;
	
	public AmountDiscountMovie(String title, Duration runningTime, Money fee, Money discountAmount, DiscountCondition... discountConditions){
	super(title,runningTime,fee,discountConditions);
	this.percent=percent;	
	}
	
	@Override
	protected Money calculateDiscountAmount(){
		return getFee().times(percent);
	}
}

```

### 리펙터링(Refactoring)
: 결과의 변경 없이 코드의 구조를 재조정 

**몬스터 메서트(monster method)**: 낮은 응집도와 긴 호흡으로 인해 이해하기 어렵고 재사용하기도 어려우며 변경하기도 어려운 메서드 

### ==HANDS ON

```
public class ReservationAgency{
	public Reservation reserve(Screening screening, Customer customer, int audienceCount){
		Movie movie= screening.getMovie();
		boolean discountable=false;
		for(DiscountCondition condition : movie.getDiscountConditions()){
			if(condition.getType()==DiscountConditionType.PERIOD){
				discountable=screening.getWhenScreened().getDayOfWeek().equals(condition.getDatOfWeek())&&condition.getStartTime().compareTo(screening.getWhenScreened().toLocalTime())<=0&& condition.getEndTime().compareTo(screening.getWhenScreened().toLocalTime())>=0;
			}else{
			discountable=condition.getSequence()==screening.getSequence();
			}
			
			if(discountable){
				break;
			}
		}

	Money fee;
	if(discountalbe){
		Money discountAmount=Money.ZERO;
		switch(movie.getMovieType()){
			case AMOUNT_DISCOUNT:
				discountAmount=movie.getDiscountAmount();
				break;
			case PERCENT_DISCOUNT:
				discountAmount=movie.getFee().times(movie.getDiscountPercent());
				break;
			case NONE_DISCOUNT:
				discountAmount=Money.ZERO;
				break;	
		}
		fee=movie.getFee().minus(discountAmount).time(audienceCount);
	}else {
		fee= movie.getFee().times(audienceCount);
	}
	return new Reservation(customer,screening,fee,audienceCount);
	}
}
```

위의 reserve 메서드는 몬스터 메서드의 형태입니다.

reserve 매서드를 아래와 같이 작성되어 있을 시 동일한 작업을 수행하도록
높은 응집도를 가진 작은 함수들로 분리하여 리팩토링 해봅시다!


```
public Reservateion reserve(Screening screening, Customer customer, int audienceCount){
	boolean discountable=checkDiscountalbe(screening);
	Money fee=calculateFee(screening,discountalbe,audienceCount);
	return createReservation(screening, customer,audienceCount,fee);
}
```