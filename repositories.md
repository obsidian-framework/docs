# Repositories

Separate your data access logic from your controllers with the Repository pattern.

## Creating a Repository

```java
@Repository
public class ArticleRepository {

    public LazyList<Article> findAll() {
        return Article.findAll();
    }

    public LazyList<Article> findPublished() {
        return Article.where("status = ?", 1);
    }

    public LazyList<Article> findByAuthor(Integer userId) {
        return Article.where("user_id = ?", userId);
    }

    public Article findById(Integer id) {
        return Article.findById(id);
    }

    public void create(String title, String content) {
        Article article = new Article();
        article.setTitle(title);
        article.setContent(content);
        article.setStatus(1);
        article.saveIt();
    }
}
```

Repositories annotated with `@Repository` are automatically discovered and available for injection in your controllers. See [Controllers — Dependency Injection](controllers.md#dependency-injection).

> **Best Practice:** Use repositories for any complex data retrieval logic. It keeps your controllers clean and makes testing easier.
