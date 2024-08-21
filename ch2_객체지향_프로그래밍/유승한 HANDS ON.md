
```java
public class ArticleResponse {  
  
    private String title;  
  
    private int content;  
  
    private LocalDateTime createdAt;  
  
    private LocalDateTime modifiedAt;  
  
    public ArticleResponse(String title, int content, LocalDateTime createdAt) {  
        this.title = title;  
        this.content = content;  
        this.createdAt = createdAt;  
    }  
      
    public String getTitle() { return this.title; }  
    public int getContent() { return this.content; }  
    public LocalDateTime getCreatedAt() { return this.createdAt; }  
}
```

1. 추후에 멤버 변수 추가 시에 생성자 및 빌더 패턴으로 인하여 추후 수정할 코드들이 많아질 수 있음
2. 따라서 빌더 패턴을 삭제함으로써 하나의 생성자로 생성 시키며 빌더 패턴 같은 경우에 멤버 변수를 선별적으로 선택이 가능해 생기는 문제(생성자에서 몇가지 멤버변수가 빠지는 등)을 컴파일 단계에서 잡을 수 있음