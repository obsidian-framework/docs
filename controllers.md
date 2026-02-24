# Controllers

All controllers must extend `BaseController` and carry the `@Controller` annotation.

## Basic Controller

```java
@Controller
public class ArticleController extends BaseController {

    @GET(value = "/articles", name = "articles.index")
    private Object index(Request req, Response res) {
        return render("articles/index.html", Map.of(
            "title", "My articles"
        ));
    }

    @POST(value = "/articles", name = "articles.store")
    private Object store(Request req, Response res) {
        String title = req.queryParams("title");

        Article article = new Article();
        article.set("title", title);
        article.saveIt();

        res.redirect("/articles");
        return null;
    }
}
```

## Dependency Injection

You can inject any class annotated with `@Repository` directly as a method parameter:

```java
@GET(value = "/articles", name = "articles.index")
private Object index(ArticleRepository articleRepo) {
    List<Article> articles = DB.withConnection(() ->
        articleRepo.findAll().stream().toList()
    );

    return render("articles/index.html", Map.of(
        "articles", articles
    ));
}
```

> **Tip:** You can inject as many dependencies as needed directly in the method signature.
