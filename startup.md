# Application Startup

Obsidian uses a convention-over-configuration approach with automatic class discovery. One line of code starts everything.

## Entry Point

```java
// Main.java
public class Main {
    public static void main(String[] args) throws Exception {
        Obsidian.run(Main.class);
    }
}
```

## Auto-Discovery

When you call `Obsidian.run(Main.class)`, the framework scans the package of your `Main` class and all its subpackages for annotated components. No XML configuration, no manual registration.

| Annotation | Role |
|---|---|
| `@Controller` | Classes with route handlers (`@GET`, `@POST`, etc.) |
| `@Repository` | Data access classes available for dependency injection |
| `@UserDetailsServiceImpl` | Authentication service implementation |
| `@GlobalAdvice` | Global interceptors executed before every request |

## Recommended Project Structure

While you're free to organize your code however you want, the following structure is recommended:

```
src/main/java/
└── com.yourcompany.yourapp/
    ├── Main.java
    ├── controllers/
    │   ├── ArticleController.java
    │   └── UserController.java
    ├── models/
    │   ├── Article.java
    │   └── User.java
    ├── repositories/
    │   ├── ArticleRepository.java
    │   └── UserRepository.java
    ├── websockets/
    │   └── ChatHandler.java
    ├── middlewares/
    │   └── CustomMiddleware.java
    └── services/
        └── AppUserDetailsService.java
```

> **Note:** The framework scans the package where your `Main.class` is located and all its subpackages. Organize subpackages however you want.
