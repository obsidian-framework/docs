# Security & Authentication

The authentication system is built around `UserDetailsService`, an abstraction that lets you organize your User model however you want.

## 1. Create your UserDetailsService

```java
@UserDetailsServiceImpl
public class AppUserDetailsService implements UserDetailsService {

    private final UserRepository userRepository;

    public AppUserDetailsService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @Override
    public UserDetails loadByUsername(String username) {
        return DB.withConnection(() -> {
            if (!UserRepository.userExist(username)) return null;
            User user = userRepository.findByUsername(username);
            return adapt(user);
        });
    }

    @Override
    public UserDetails loadById(Object id) {
        return DB.withConnection(() -> {
            User user = User.findById(id);
            return adapt(user);
        });
    }

    private UserDetails adapt(User user) {
        if (user == null) return null;

        return new UserDetails() {
            public Object getId()       { return user.getId(); }
            public String getUsername() { return user.getUsername(); }
            public String getPassword() { return user.getPassword(); }
            public String getRole()     { return user.getRole(); }
            public boolean isEnabled()  { return true; }
        };
    }
}
```

The `@UserDetailsServiceImpl` annotation is all the framework needs to detect your implementation. No manual configuration required.

## 2. Authentication in Controllers

```java
@Controller
public class LoginController extends BaseController {

    @POST("/users/login")
    private Object loginBack(Request req, Response res) {
        String username = req.queryParams("username");
        String password = req.queryParams("password");
        Session session = req.session(true);

        if (login(username, password, session)) {
            res.redirect("/dashboard");
            return true;
        }

        return redirectWithFlash(req, res, "error", "Incorrect login", "/users/login");
    }

    @HasRole("DEFAULT")
    @GET("/users/logout")
    private Object logout(Request req, Response res) {
        logout(req.session(true));
        return redirectWithFlash(req, res, "success", "Logged out", "/users/login");
    }
}
```

## Available Methods in BaseController

```java
// Authentication
login(username, password, session)   // Authenticate and create session
logout(session)                      // Log out the user

// Checks
isLogged(req)                        // Check if logged in
hasRole(req, "ADMIN")                // Check a specific role
requireLogin(req, res)               // Force login (redirects otherwise)

// Retrieval
getLoggedUser(req)                   // Get the UserDetails object
```

## Role-Based Protection

```java
@HasRole("ADMIN")
@GET("/admin/dashboard")
private Object adminDashboard(Request req, Response res) {
    return render("admin/dashboard.html", Map.of());
}

@HasRole("DEFAULT")
@GET("/profile")
private Object profile(Request req, Response res) {
    UserDetails user = getLoggedUser(req);
    return render("profile.html", Map.of("user", user));
}
```

> **Note:** The `"DEFAULT"` role means "any logged-in user".

## Adding Custom Fields

You can extend `UserDetails` with your own interface to expose additional fields:

```java
// 1. Create your custom interface
public interface AppUserDetails extends UserDetails {
    String getEmail();
    String getIp();
}

// 2. Adapt to this interface
private AppUserDetails adapt(User user) {
    if (user == null) return null;

    return new AppUserDetails() {
        public Object getId()       { return user.getId(); }
        public String getUsername() { return user.getUsername(); }
        public String getPassword() { return user.getPassword(); }
        public String getRole()     { return user.getRole(); }
        public String getEmail()    { return user.getEmail(); }
        public String getIp()       { return user.getIp(); }
    };
}

// 3. Use in controllers
@GET("/profile")
private Object profile(Request req, Response res) {
    AppUserDetails user = getLoggedUser(req);
    String email = user.getEmail(); // ✅ Available!

    return render("profile.html", Map.of("user", user));
}
```
