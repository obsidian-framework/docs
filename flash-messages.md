# Flash Messages

Display temporary notifications after user actions. No external dependencies required.

## Usage in a Controller

```java
@POST("/login")
public Object login(Request req, Response res)
{
    String username = req.queryParams("username");
    String password = req.queryParams("password");

    if (login(username, password, req.session())) {
        return redirectWithFlash(req, res, "success", "Welcome back!", "/dashboard");
    }

    return redirectWithFlash(req, res, "error", "Invalid credentials", "/login");
}
```

## Notification Types

```java
redirectWithFlash(req, res, "success", "Operation completed!", "/");
redirectWithFlash(req, res, "error",   "Something went wrong",  "/");
redirectWithFlash(req, res, "info",    "Check your email",      "/");
redirectWithFlash(req, res, "warning", "Changes require restart", "/");
```

## Displaying in a Template

Place `{{ flash() }}` anywhere in your base layout — typically just before the closing `</body>` tag:

```twig
<!DOCTYPE html>
<html>
<body>
    <nav>...</nav>

    {% block content %}{% endblock %}

    {{ flash() | raw }}
</body>
</html>
```

## Custom Configuration

Use a `@Config` class to customize flash behavior (see [Configuration](config.md)):

```java
// Duration (ms)
FlashConfig.setDuration(5000);

// Position
// Options: top-right, top-left, bottom-right, bottom-left, top-center, bottom-center
FlashConfig.setPosition("top-right");

// Custom CSS
FlashConfig.setCustomCSS("""
    .flash-notification {
        border-radius: 1rem;
        font-size: 0.875rem;
    }
    .flash-success {
        background: linear-gradient(to right, #10b981, #059669);
    }
""");
```
