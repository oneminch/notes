- The practice of recording information about a program's execution for debugging, performance monitoring, and auditing purposes.
- An important part of software development and maintenance.
- It's important to:
    - be mindful of sensitive information in logs and implementing appropriate safeguards.
    - ensure that logging doesn't significantly impact application performance, often through asynchronous logging or buffering.

- **Log Levels** are used by Logging systems to categorize log messages different severity levels.
    - Common levels include:
        - TRACE/FINEST: Most detailed information
        - DEBUG: Diagnostic information
        - INFO: General information
        - WARN: Potential issues that aren't errors
        - ERROR: Error events that might still allow the application to continue
        - FATAL/SEVERE: Critical errors that cause the application to abort

```java
import java.util.logging.Logger;
import java.util.logging.Level;

public class LogLevelExample {
    private static final Logger LOGGER = Logger.getLogger(LogLevelExample.class.getName());

    public static void main(String[] args) {
        LOGGER.finest("FINEST level message");
        LOGGER.finer("FINER level message");
        LOGGER.fine("FINE level message");
        LOGGER.config("CONFIG level message");
        LOGGER.info("INFO level message");
        LOGGER.warning("WARNING level message");
        LOGGER.severe("SEVERE level message");
    }
}
```

- **Loggers** are objects responsible for capturing log events and routing them to appropriate destinations.
    - Are often organized hierarchically.
- **Appenders / Handlers** are components that determine where log messages are sent.
    - Common destinations include console, files, databases, and external logging services.
- **Layouts / Formatters** are responsible for converting and formatting log data into a specific output format (e.g., plain text, JSON, XML).

## Best Practices

> [!example]- 🎥 Logging Best Practices (YouTube)
> ![12 Logging BEST Practices in 12 minutes (YouTube)](https://www.youtube.com/watch?v=I2mWnh66Bkg)
