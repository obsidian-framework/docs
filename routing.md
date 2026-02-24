# Routing

No more manually declared routes. Use annotations directly on your controller methods.

## HTTP Methods

```java
@GET(value = "/articles", name = "articles.index")
@POST(value = "/articles", name = "articles.store")
@PUT(value = "/articles/:id", name = "articles.update")
@DELETE(value = "/articles/:id", name = "articles.delete")
@PATCH(value = "/articles/:id", name = "articles.patch")
```

## Route Parameters

```java
@GET(value = "/articles/:id", name = "articles.show")
private Object show(Request req, Response res) {
    String id = req.params(":id");
    // ...
}
```

## Query Parameters

```java
@GET(value = "/search", name = "search")
private Object search(Request req, Response res) {
    String query = req.queryParams("q");
    String page  = req.queryParams("page");
    // ...
}
```

## Named Routes

Use the `name` parameter to reference routes by name instead of hardcoding paths.

```java
@GET(value = "/articles/:id", name = "articles.show")
private Object show(Request req, Response res) {
    // ...
}
```

In your Pebble templates:

```twig
<a href="{{ route(name='articles.show') }}">View Article</a>

<form action="{{ route(name='articles.store') }}" method="post">
    <!-- form fields -->
</form>
```

Programmatic access:

```java
String path       = Route.getPath("articles.show");  // "/articles/:id"
boolean exists    = Route.hasRoute("articles.show");  // true
Map<String, String> all = Route.getAllRoutes();        // all named routes
```
