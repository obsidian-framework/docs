# Database Configuration

Obsidian uses **ActiveJDBC** as its ORM layer and **HikariCP** for connection pooling. The `DB` class provides a unified interface for managing database connections and transactions across SQLite, MySQL, and PostgreSQL.

## Supported Databases

| Database | Notes |
|---|---|
| **SQLite** | File-based, perfect for development and small applications |
| **MySQL** | Popular relational database, uses connection pooling |
| **PostgreSQL** | Advanced relational database with robust features |

## Environment Configuration

Obsidian uses **dotenv-java** to manage database credentials. Create a `.env` file at your project root:

> **Warning:** Never commit your `.env` file to version control. Add it to `.gitignore`.

**SQLite**

```env
ENVIRONMENT=development
PORT_WEB=8080

DB_TYPE=sqlite
DB_PATH=database.db
```

**MySQL**

```env
ENVIRONMENT=development
PORT_WEB=8080

DB_TYPE=mysql
DB_HOST=localhost
DB_PORT=3306
DB_NAME=my_database
DB_USER=root
DB_PASSWORD=secret
```

**PostgreSQL**

```env
ENVIRONMENT=development
PORT_WEB=8080

DB_TYPE=postgresql
DB_HOST=localhost
DB_PORT=5432
DB_NAME=my_database
DB_USER=postgres
DB_PASSWORD=secret
```

The framework automatically reads these variables at startup. No code changes needed — just modify your `.env` file and restart your application.

## Connection Management

All database operations must be wrapped in `DB.withConnection()` or `DB.withTransaction()`. This ensures proper connection lifecycle management.

**Simple query**

```java
List<Article> articles = DB.withConnection(() ->
    Article.findAll().stream().toList()
);
```

**Single operation**

```java
Article article = DB.withConnection(() -> {
    Article newArticle = new Article();
    newArticle.set("title", "Getting Started");
    newArticle.set("content", "Welcome to Obsidian");
    newArticle.saveIt();
    return newArticle;
});
```

**Multiple operations**

```java
DB.withConnection(() -> {
    List<Article> articles = Article.where("published = ?", true);

    for (Article article : articles) {
        article.set("views", article.getInteger("views") + 1);
        article.saveIt();
    }

    return articles.size();
});
```

## Transactions

Use `DB.withTransaction()` when you need atomic operations. Changes are automatically committed on success or rolled back on error.

```java
DB.withTransaction(() -> {
    User sender = User.findById(senderId);
    User receiver = User.findById(receiverId);

    if (sender.getBigDecimal("balance").compareTo(amount) < 0) {
        throw new IllegalStateException("Insufficient balance");
    }

    sender.set("balance", sender.getBigDecimal("balance").subtract(amount));
    receiver.set("balance", receiver.getBigDecimal("balance").add(amount));

    sender.saveIt();
    receiver.saveIt();

    return true;
});
```

## Connection Pool (MySQL / PostgreSQL)

HikariCP is automatically configured for MySQL and PostgreSQL with optimized defaults:

| Setting | Value |
|---|---|
| Maximum Pool Size | 20 connections |
| Minimum Idle | 5 connections |
| Auto-commit | Disabled (managed by ActiveJDBC) |

> **Note:** SQLite uses direct JDBC connections without pooling.
