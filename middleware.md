# Middleware

Intercept requests before and after controller execution with simple annotations.

## Basic Usage

```java
@Before(LoggingMiddleware.class)
@GET(value = "/dashboard", name = "dashboard")
private Object dashboard(Request req, Response res) {
    return render("dashboard.html", Map.of());
}
```

## Chaining Multiple Middlewares

```java
@Before({CorsMiddleware.class, ApiKeyMiddleware.class, RateLimitMiddleware.class})
@GET(value = "/api/data", name = "api.data")
private Object data(Request req, Response res) {
    return "{ \"data\": [] }";
}
```

## Before and After

```java
@Before(TimingMiddleware.class)
@After(TimingMiddleware.class)
@GET(value = "/profile", name = "profile")
private Object profile(Request req, Response res) {
    return render("profile.html", Map.of());
}
```

## Built-in Middlewares

| Middleware | Description | Useful for |
|---|---|---|
| `LoggingMiddleware` | Logs each request with IP and User-Agent | Debugging, monitoring, audit trail |
| `CorsMiddleware` | Adds CORS headers for cross-origin requests | Public APIs, separate frontends, mobile apps |
| `RateLimitMiddleware` | Limits to 100 req/min per IP, returns 429 if exceeded | Anti-spam, anti-DDoS, public endpoints |
| `TimingMiddleware` | Measures execution time, adds `X-Response-Time` header | Performance optimization, detecting slow routes |
| `ApiKeyMiddleware` | Verifies `X-API-Key` header, returns 401 if invalid | Securing APIs, simple authentication |

## Creating a Custom Middleware

```java
public class MaintenanceMiddleware implements Middleware {

    @Override
    public void handle(Request req, Response res) throws Exception {
        boolean isMaintenance = System.getenv("MAINTENANCE_MODE") != null;

        if (isMaintenance && !req.pathInfo().equals("/maintenance")) {
            res.redirect("/maintenance");
            throw new Exception("Maintenance mode");
        }
    }
}
```

```java
@Before(MaintenanceMiddleware.class)
@GET("/dashboard")
private Object dashboard(Request req, Response res) {
    return render("dashboard.html", Map.of());
}
```

## Headers Added Automatically

```
# TimingMiddleware
X-Response-Time: 42ms

# RateLimitMiddleware
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 87

# CorsMiddleware
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization, X-CSRF-TOKEN
```

## Generated Logs (LoggingMiddleware)

```
INFO: GET /articles - IP: 192.168.1.1 - User-Agent: Mozilla/5.0...
INFO: POST /api/users - IP: 10.0.0.5 - User-Agent: PostmanRuntime/7.26.8
INFO: DELETE /articles/42 - IP: 192.168.1.1 - User-Agent: Mozilla/5.0...
```

## Execution Order

- `@Before` middlewares execute in array order
- Controller executes next
- `@After` middlewares execute after the controller
- If a middleware throws an exception, execution stops
- Middlewares are instantiated once (singleton)
