# Configuration

You can configure framework features by creating a class annotated with `@Config` that implements `ConfigInterface`.

```java
@Config
public class NotifConfig implements ConfigInterface
{
    @Override
    public void configure()
    {
        FlashConfig.setDuration(5000);

        FlashConfig.setPosition("top-right");

        FlashConfig.setCustomCSS("""
            .flash-notification {
                border-radius: 1rem;
                font-size: 0.875rem;
            }
            .flash-success {
                background: linear-gradient(to right, #10b981, #059669);
            }
        """);
    }
}
```

The `@Config` class is automatically discovered and executed at startup. No manual registration needed.

See the [Flash Messages](flash-messages.md) page for all available `FlashConfig` options.
