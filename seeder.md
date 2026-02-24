# Database Seeder

Seeders let you populate your database with initial or test data. They are automatically discovered and executed at startup.

## Creating a Seeder

```java
@Seeder(priority = 10)
public class PostSeeder implements SeederInterface
{
    @Inject
    private PostRepository postRepository;

    @Override
    public void seed()
    {
        DB.withConnection(() -> {
            if (postRepository.findAll().isEmpty()) {
                postRepository.create(
                    "fore-magna-nostris-et-propinqua",
                    "Fore magna nostris et propinqua.",
                    "Ultima Syriarum est Palaestina per intervalla magna protenta abundans.",
                    "admin"
                );
            }
            return null;
        });
    }
}
```

## Priority

The `priority` attribute controls execution order. Seeders with a lower value run first. This is useful when some seeders depend on data created by others (e.g. a `UserSeeder` must run before a `PostSeeder`).

```java
@Seeder(priority = 1)   // runs first
public class UserSeeder implements SeederInterface { ... }

@Seeder(priority = 10)  // runs after
public class PostSeeder implements SeederInterface { ... }
```
