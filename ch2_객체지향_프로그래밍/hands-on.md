### 문제점
빌더 패턴은 값을 하나씩 입력받은 후 최종적으로 build() 메소드로 하나의 인스턴스를 생성하여 리턴하는 패턴이다. 

`builder` 메서드는 클래스의 특정 속성(변수)만을 포함시켜서 인스턴스를 생성할 수도 있다. 따라서 멤버 변수를 새로 추가한다면 해당 멤버변수의 값을 입력하기 위해선 빌더 패턴을 사용한 모든 코드들을 수정해줘야 한다. 이는 OCP(개방-폐쇄 원칙)을 위반한다.

### 해결방법

해결 방법 1. (객체 생성시 기본 값을 가져도 된다면) 새로 추가되는 멤버 변수에 기본 값 제공

```java
@Builder.Default
private String newField = "new";
```

해결 방법 2. 빌더 패턴 대신 생성자 사용

```java
public ArticleResponse(String title, int content, LocalDateTime createdAt, String newField) {
		this.title = title;
		this.content = content;
		this.createdAt = createdAt;
		this.newField = newField;
	}
```

해결 방법 3. 추가되는 멤버변수를 필수 필드로 지정
```java
@NonNull
private String newField;
```