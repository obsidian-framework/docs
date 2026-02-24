# CSRF Protection

Obsidian provides automatic protection against Cross-Site Request Forgery attacks.

## Protecting a Route

Add the `@CsrfProtect` annotation to any mutating route:

```java
@CsrfProtect
@POST(value = "/articles", name = "articles.store")
private Object store(Request req, Response res) {
    // Token is validated automatically.
    // If invalid → 403 Forbidden
}
```

## HTML Forms

Use the `csrf_field()` template function to inject the hidden token field:

```twig
<form method="POST" action="/articles">
    {{ csrf_field() | raw }}

    <input type="text" name="title">
    <button type="submit">Create</button>
</form>
```

## AJAX Requests

For JavaScript requests, read the token with `csrf_token()` and send it as a header:

```twig
{# In your template — make the token available to JS #}
const csrfToken = '{{ csrf_token() }}';
```

```javascript
fetch('/articles', {
    method: 'POST',
    headers: {
        'X-CSRF-TOKEN': csrfToken,
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({ title: 'My article' })
});
```

## Regenerating the Token

Always regenerate the CSRF token after a successful login to prevent session fixation:

```java
@POST("/login")
private Object login(Request req, Response res) {
    if (isLogged(req)) {
        regenerateCsrfToken(req);
        res.redirect("/dashboard");
    }
    return null;
}
```

## Behavior

- `GET`, `HEAD`, and `OPTIONS` requests are never protected
- `POST`, `PUT`, `DELETE`, and `PATCH` routes require the `@CsrfProtect` annotation to be validated
- Token expires after 1 hour of inactivity
- Token can be sent via the `X-CSRF-TOKEN` header or the `_csrf` hidden field
