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
> **S3는 파일 서버인 만큼 트래픽 분배가 주요하지만, 개발자는 이를 신경쓸 필요 없다. AWS에서 자체적으로 이러한 처리를 전부 핸들링해준다.**
