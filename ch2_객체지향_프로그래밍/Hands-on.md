
---

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
---
💡@Builder 어노테이션은 필요한 필드를 선택적으로 지정하여 객체를 생성할 수 있다.
### 발단
클라이언트에서 **modifiedAt** 필드를 추가해달라고 하는 상황
### 전개
작업자는 단순히 필드를 추가하려고 함.
### 위기
생각해보니 @Builder를 사용한 코드들이 있음. 
이로 인해 modifiedAt 필드가 null인 상태로 객체가 생성될 수 있는 상황이 발생. modifiedAt 필드를 사용하려고 하면 오류가 발생할 수 있음.
빌더 패턴은 컴파일 단계에서 오류를 발견할 수 없음 omg
### 절정
ArticleResponse 객체를 @Builder를 사용하여 생성하는 코드가 존재하지만, 
작업자는 모든 코드를 파악하고 있지 않으며 수정하자니 작업량이 너무 많음.
### 결말
빌더 포기하기 or 새로 추가되는 필드를 필수로 지정해주기

```java
@Builder
public class ArticleResponse {

	private String title;

	private int content;

	private LocalDateTime createdAt;

	@NonNull
	private LocalDateTime modifiedAt;

	public ArticleResponse(String title, int content, LocalDateTime createdAt, LocalDateTime modifiedAt) {
		this.title = title;
		this.content = content;
		this.createdAt = createdAt;
		this.modifiedAt = modifiedAt;
	}

	public static ArticleResponse create(Article article) {
		return ArticleResponse.builder()
				.title(article.getTitle())
				.content(article.getContent())
				.createdAt(article.getCreatedAt())
				.modifiedAt(article.getModifiedAt())
				.build();
	}

	public String getTitle() { return this.title; }
	public int getContent() { return this.content; }
	public LocalDateTime getCreatedAt() { return this.createdAt; }
	public LocalDateTime getModifiedAt() { return this.modifiedAt; }
}
```