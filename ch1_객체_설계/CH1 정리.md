### 티켓 판매 애플리케이션 구현

1. 초대장이 있을 시 티켓으로 교환 후 입장이 가능하다
2. 초대장이 없을 시 티켓을 구매 후 입장이 가능하다
```java
public class Invitation {
    private LocalDateTime when;
}

public class Ticket {
    private Long fee;

    public Long getFee() {
        return fee;
    }
}
```

3. 관람객의 소지품은 초대장, 현금, 티켓 세 가지이며 가방에 들어가 있다. Bag 인스턴스 상태는 현금과 초대장을 함께 보관하거나, 초대장 없이 현금만 보관하는 상태 중 하나이다.
```java

public class Bag {
    private Long amount;
    private Invitation invitation;
    private Ticket ticket;
	
	// Bag 인스턴스 상태는 현금과 초대장을 함께 보관하거나, 
    public Bag(long amount) {
        this(null, amount);
    }
	// 초대장 없이 현금만 보관하는 상태 중 하나
    public Bag(Invitation invitation, long amount) {
        this.invitation = invitation;
        this.amount = amount;
    }

    public boolean hasInvitation() {
        return invitation != null;
    }

    public boolean hasTicket() {
        return ticket != null;
    }

    public void setTicket(Ticket ticket) {
        this.ticket = ticket;
    }

    public void minusAmount(Long amount) {
        this.amount -= amount;
    }

    public void plusAmount(Long amount) {
        this.amount += amount;
    }
}
```

4. 관람객은 소지품 보관하기 위한 가방을 소지할 수 있다.
```java
public class Audience {
    private Bag bag;

    public Audience(Bag bag) {
        this.bag = bag;
    }

    public Bag getBag() {
        return bag;
    }
}
```

5. 매표소에서 초대장을 티켓으로 교환하거나 구매 가능하다. 매표소는 판매하거나 교환해 줄 티켓의 목록과 판매 금액을 인스턴스 변수로 포함한다.
```java
public class TicketOffice {
    private Long amount;
    private List<Ticket> tickets = new ArrayList<>();

    public TicketOffice(Long amount, Ticket ... tickets) {
        this.amount = amount;
        this.tickets.addAll(Arrays.asList(tickets));
    }
	
	// 편의을 위해 맨 앞의 티켓을 반환해준다
    public Ticket getTicket() {
        return tickets.remove(0);
    }

    public void minusAmount(Long amount) {
        this.amount -= amount;
    }

    public void plusAmount(Long amount) {
        this.amount += amount;
    }
}
```

6. 판매원은 매표소에서 초대장을 티켓으로 교환하거나 티켓 판매하는 역할 수행하며 자신이 일하는 매표소를 알고 있어야 한다.
```java
public class TicketSeller {
    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public TicketOffice getTicketOffice() {
        return ticketOffice;
    }
}
```

7. 소극장 클래스는 관람객을 맞이할 수 있도록 enter 메서드를 수행한다.
```java
public class Theater {
    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

    public void enter(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}
```

![[Pasted image 20240706100648.png]]

---

### 설계 문제점


> [!소프트웨어 모듈이 가져야 하는 세 가지 기능]
> **1. 실행 중에 제대로 동작해야 한다.**
> **2. 변경에 용이해야 한다.**
> **3. 이해하기 쉬워야 한다.**


#### 1. 예상을 빗나가는 코드

- 관람객과 판매원이 소극장의 통제를 받는 수동적인 클래스
- Theater의 enter 메서드를 이해하기 위해서는 Audience가 Bag을 가지고 있고, Bag 안에 ,,,, 하나의 클래스 혹은 메서드에 너무 많은 세부사항을 다루고 있다.

#### 2. 변경에 취약한 코드

- 객체 사이의 불필요한 **의존성**이 다수 존재해 변경 시 많은 부분이 변경 된다.
- 객체 사이의 의존성이 과한 경우를 **결합도(coupling)** 이 높다고 말한다.

> [!설계 목표]
> **객체 사이의 결합도를 낮춰 변경이 용이한 설계를 만드는 것**


### 설계 개선하기

예제 코드는  **실행 중에 제대로 동작**한다는 조건을 만족시키지만 다른 두개의 조건(**변경이 어렵고 이해하기 쉽지 않다.**)을 만족시키지 못한다.

**개선 방안**
	현재 예제 코드는 관람객과 판매원이 자신의 일을 스스로 처리해야 한다는 우리의 직관을 벗어나기 때문.
	 
	 ** 즉, 객체를 자율적인 존재로 만들어야 한다 ** 



#### 자율성을 높이자

- Theater가 Audience와 TicketSeller 접근
	-> Audiecne와 TicketSeller가 직접 Bag와 TicketOffice를 처리하게끔 수정
	-> ```sellTo``` 함수
- TicketSeller와 동일한 방법으로 Audience의 캡슐화
	-> Bag에 접근하는 모든 로직을 Audience 내부로 감추기
	-> ```buy``` 함수

*기존 enter 함수*
``` java
	public void enter(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
```

*개선*
```java
public class Theater {
    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

	
    public void enter(Audience audience) {
        ticketSeller.sellTo(audience);
    }
}

public class TicketSeller {
    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public void sellTo(Audience audience) {
        ticketOffice.plusAmount(audience.buy(ticketOffice.getTicket()));
    }
}

public class Audience {
    private Bag bag;

    public Audience(Bag bag) {
        this.bag = bag;
    }

    public Long buy(Ticket ticket) {
        if (bag.hasInvitation()) {
            bag.setTicket(ticket);
            return 0L;
        } else {
            bag.setTicket(ticket);
            bag.minusAmount(ticket.getFee());
            return ticket.getFee();
        }
    }
}
```

- TicketSeller에서 getTicketOffice 메서드가 제거
	-> ticketOffice의 가시성 private, 오직 TicketSeller 안에서 접근 가능
	->TicketSeller는 ticketOffice 관련 작업을 직접 수행하게 됨 (sellTo)
-  Audience에서 getBag 메서드 제거
	-> bag 가시성 private, 오직 Audience 안에서 접근 가능
	->Audience는 bag 관련 작업을 직접 수행하게 됨 (buy)

> [!캡슐화]
> **객체 내부의 세부적인 사항을 감추는 것**
> **캡슐화를 통해 객체 내부 접근을 제한해 결합도를 낮출 수 있음**

![[Pasted image 20240706151025.png]]
**개선 점**
	Theater는 오직 TicketSeller와 Audience의 **인터페이스**에 의존
	(TicketSeller가 내부에 TicketOffice 인스턴스를 포함하고 있다는 사실과 Audience가 Bag 인스턴스를 포함하고 있다는 사실은 **구현** 영역)

	**Audience나 TicketSeller의 내부 구현을 변경하더라도 Theater를 함께 변경할 필요가 없어짐**

> [!캡슐화와 응집도]
> **객체 내부의 상태를 캡슐화하고 
> 객체 간에 오직 메시지를 통해서만 상호작용하는 것이 핵심**
> 
> **자율적인 객체는 응집도가 높다 할 수 있음**

(밀접하게 연관된 작업만을 수행하고 연관성 없는 작업은 다른 객체에게 위임하게 될 시
해당 객체는 자율적이다)

#### 절차지향과 객체지향

**절차지향**
	Theater의 enter 메서드는 **프로세스**이며 각 클래스(Audience 등)는 **데이터**이다.
	이처럼 **프로세스와 데이터를 별도의 모듈에 위치**시키는 방식이 **절차적 프로그래밍**
	**그림 1.2와 같은 의존성 구조**를 보여주며 **변경하기 어려운 코드를 만든다**는 단점 존재
	모든 처리가 하나의 클래스 안에 위치하고 나머지 클래스는 단지 데이터의 역할만 수행하기 때문

**객체지향**
	데이터와 프로세스가 동일한 모듈 내부에 위치하도록 프로그래밍하는 방식을 **객체지향 프로그래밍**
	**캡슐화**를 이용해 의존성을 적절히 관리함으로써 객체 사이의 **결합도를 낮추는게 좋은 객체지향 설계**
	객체 내부의 변경이 객체 외부에 파급되지 않도록 제어해야 함

**책임의 이동**
	절차지향과 객체지향의 근본적인 차이점
	'**책임'이란 기능**
	![[Pasted image 20240706153009.png]]
	![[Pasted image 20240706153022.png]]
	변경 전에는 절차지향적으로 책임이 Theater에 집중되어 있지만
	변경 후에는 객체지향적으로 책임이 개별 객체로 이동한 것

> [!객체지향적 설계는 ,,,]
> **캡슐화를 통해 객체의 자율성을 높이고 응집도 높은 객체들의 공동체를 창조하는 것**



### 스프링을 통해 알아보기

#### 역할과 구현을 분리

역할과 구현으로 구분하면 세상이 단순해지고 유연해지며 변경도 편리해진다.

**장점**

- 클라이언트는 구현 대상의 내부 구조를 몰라도 된다
- 클라이언트는 구현 대상의 내부 구조가 변경되어도 영향을 받지 않는다
- 클라이언트는 구현 대상 자체를 변경해도 영향을 받지 않는다

**자바언어**

- 자바언어의 다형성을 활용
    - 역할 = 인터페이스
    - 구현 = 인터페이스를 구현한 클래스, 구현 객체
- 객체를 설계할 때 역할과 구현을 명확히 분리
- 객체 설계 시 역할을 먼저 부여하고, 그 역할을 수행하는 구현 객체 만들기

**정리**

- 실세계의 역할과 구현이라는 편리한 컨셉을 다형성을 통해 객체 세상으로 가져올 수 있음
- 유연하고 변경이 용이
- 확장 가능한 설계
- 클라이언트에 영항을 주지 않는 변경 가능
- 인터페이스를 안정적으로 잘 설계하는 것이 중요

#### 역할과 구현을 분리 한계

- 인터페이스 자체가 변하면 클라이언트 서버 모두에 큰 변경이 발생
- 인터페이스를 안정적으로 잘 설계하는 것이 중요

#### 스프링과 객체 지향

- 스프링은 다형성을 극대화해서 이용할 수 있게 도움
- 스프링에서 이야기하는 제어의 역전IoC 의존관계 주입DI은 다형성을 활용해서 역할과 구현을 편리하게 다룰 수 있도록 지원


#### 다형성이란?
![[다형성_예시_1.png]]
- **구현의 대체 가능성**을 의미
- 이것이 **유연하고 변경이 용이**하다고 표현

#### 의존성 주입(d)

**OCP 개방-폐쇄의 원칙** 
- 소프트웨어 요소는 확장에는 열려있으나 변경에는 닫혀있어야한다
- 확장을 하더라도 기존의 코드 변경 X
- 다형성을 활용

![[Pasted image 20240706154445.png]]

- **하지만 해당 예시에서는 구현 객체를 변경하려면 클라이언트 코드를 변경해야함**
- 이 문제를 해결하기 위해선 객체를 생성하고 연관관계를 맺어주는 별도의 조립, 설정자가 필요하다 → **스프링 컨테이너**

**DIP 의존관계 역전 원칙**
- 추상화에 의존해야지 구체화에 의존하면 안된다
- 클라이언트 클래스가 구현 클래스에 의존하지말고 인터페이스에 의존해야한다.
- 구현이 아닌 역할에 의존해야한다
- 위의 MemberService 클라이언트가 구현 클래스를 직접 선택
	- MemberRepository m = new MemoryMemberRepository();
	- DIP 위반


