# Hands-on

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


`ArticleResponse` 클래스의 특정 속성(변수)만을 포함시켜서 인스턴스를 생성할 수도 있다.
-> 반드시 필요한 속성을 null로 받는 것 같은데 여기서 문제가 발생할 것 같다.
default값을 만들어야 할 것 같은데 방법은 정확히 모르겠다.
