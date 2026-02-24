# Migrations

A fluent migration system inspired by Laravel to manage your database schema.

## Creating a Table

```java
public class CreateArticlesTable extends Migration {

    @Override
    public void up() {
        createTable("articles", table -> {
            table.id();
            table.string("title").notNull();
            table.text("content").notNull();
            table.integer("status").defaultValue(1);
            table.integer("user_id");
            table.timestamps();
        });
    }

    @Override
    public void down() {
        dropTable("articles");
    }
}
```

## Available Column Types

```java
table.id()                         // PRIMARY KEY AUTO_INCREMENT
table.string("name")               // VARCHAR(255)
table.string("code", 10)           // VARCHAR(10)
table.text("content")              // TEXT
table.integer("count")             // INT
table.bigInteger("big_count")      // BIGINT
table.decimal("price", 10, 2)      // DECIMAL(10,2)
table.boolean("active")            // BOOLEAN
table.date("birth_date")           // DATE
table.datetime("event_at")         // DATETIME
table.timestamp("logged_at")       // TIMESTAMP
table.timestamps()                 // created_at + updated_at
```

## Modifiers

```java
table.string("email").notNull()
table.integer("count").defaultValue(0)
table.string("code").unique()
table.integer("user_id").foreignKey("users", "id")
```
