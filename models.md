# Models

Models use **ActiveJDBC**, a lightweight ORM that follows the ActiveRecord pattern.

## Creating a Model

```java
public class Article extends Model {

    // Getters
    public String  getTitle()   { return getString("title"); }
    public String  getContent() { return getString("content"); }
    public Integer getStatus()  { return getInteger("status"); }

    // Setters
    public void setTitle(String title)     { set("title", title); }
    public void setContent(String content) { set("content", content); }
    public void setStatus(Integer status)  { set("status", status); }
}
```

## CRUD Operations

```java
// Create
Article article = new Article();
article.setTitle("My title");
article.setContent("My content");
article.saveIt();

// Retrieve
Article article              = Article.findById(1);
LazyList<Article> articles   = Article.findAll();
LazyList<Article> published  = Article.where("status = ?", 1);

// Update
article.setTitle("New title");
article.saveIt();

// Delete
article.delete();
```

## Relations

```java
public class Article extends Model {

    // An article belongs to a user
    public User getAuthor() {
        return parent(User.class);
    }

    // An article has many comments
    public LazyList<Comment> getComments() {
        return get(Comment.class, "article_id = ?", getId());
    }
}
```
