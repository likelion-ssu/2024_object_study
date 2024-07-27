
```java
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


```java
public ReservationAgency {

	public Reservation reserve(Screening screening, 
								Customer customer, 
								int audienceCount) {
								
		boolean discountable=checkDiscountable(screening);
		Money fee=calculateFee(screening,discountable,audienceCount);
		return createReservation(screening, customer,audienceCount,fee);
	}

	private boolean checkDiscountable(Screening screening) {

		return screening.getMovie().getDiscountConditions().stream()
		.anyMatch(condition -> condition.isSatisfiedBy(screening));
	}

	private Reservation createReservation(Screening screening, Customer customer,
										 int audienceCount, Money fee) {
										 
		return new Reservation(customer, screening, fee, audienceCount);
	}
}
```

```java
public class Screening {
	
	public calculateFee(boolean discountable, int audienceCount) {
		if (discountable) {
			return movie.getFee()
				.minus(calculateDiscountedFee(movie))
				.times(audienceCount);
		}
		return movie.getFee().times(audienceCount);
	}
	
	public Money calculateDiscountedFee(Movie movie) {
		switch (movie.getMovieType()) {
			case AMOUNT_DISCOUNT:
				return calculateAmountDiscountedFee(movie);
			case PERCENT_DISCOUNT:
				return calculatePercentDiscountedFee(movie);
			case NONE_DISCOUNT:
				return calculateNoneDiscountedFee(movie);
		}
	
		throw new IllegalArgumentException();
	}
	
	private Money calculateAmountDiscountedFee(Movie movie) {
		return movie.getDiscountAmount();
	}
	
	private Money calculatePercentDiscountedFee(Movie movie) {
		return movie.getFee().times(movie.getDiscountPercent());Z
	}
	
	private Money calculateNoneDiscountedFee(Movie movie) {
		return Money.ZERO;
	}
}
```

```java
public interface DiscountCondition {

	public boolean isSatisfiedBy(Screening screening);
}
```

```java
public class PeriodCondition implements DiscountCondition {
	public boolean isSatisfiedBy(Screening screening) {
		return screening.getWhenScreened().getDayOfWeek().equals(condition.getDatOfWeek())&&condition.getStartTime().compareTo(screening.getWhenScreened().toLocalTime())<=0&& condition.getEndTime().compareTo(screening.getWhenScreened().toLocalTime())>=0;
	}
	
}

public class SequenceCondition implements DiscountCondition {
	public boolean isSatisfiedBy(Screening screening) {
		return condition.getSequence() == screening.getSequence();
	}
}
```
