# Question 4 – Flexible Logger with Overloaded Functions and Inline Helpers

## Important note about the supplied signatures

In C++, defining both `LogEvent(message)` and `LogEvent(message, severityLevel = 1, timestamp = "00:00:00")` makes a one-argument call ambiguous: both overloads are viable. The pseudocode below shows the intended educational behaviour (one argument selects the general INFO overload). A real C++ implementation must remove that ambiguity, for example by making `severityLevel` mandatory in the second overload or by removing the one-argument overload.

## 1. Structured pseudocode

```text
INLINE FUNCTION FormatPrefix(prefixTag : STRING) RETURNS STRING
    RETURN "[" + prefixTag + "] "
END FUNCTION


// Overload 1: general informational message.
FUNCTION LogEvent(message : STRING)
    formattedMessage <- FormatPrefix("INFO") + message
    OUTPUT formattedMessage
END FUNCTION


// Overload 2: severity-based event.
// The supplied specification gives defaults to severityLevel and timestamp.
// See the ambiguity note above for a C++ implementation.
FUNCTION LogEvent(
    message : STRING,
    severityLevel : INTEGER = 1,
    timestamp : STRING = "00:00:00"
)
    IF severityLevel = 1 THEN
        severityText <- "LOW"
    ELSE IF severityLevel = 2 THEN
        severityText <- "MEDIUM"
    ELSE IF severityLevel = 3 THEN
        severityText <- "CRITICAL"
    ELSE
        severityText <- "UNKNOWN"
    END IF

    OUTPUT "[" + severityText + "] @ " + timestamp +
           " : " + message
END FUNCTION


// Overload 3: hardware fault diagnostic.
FUNCTION LogEvent(errorCode : INTEGER, systemModule : STRING)
    hexCode <- ConvertIntegerToHexadecimal(errorCode)

    OUTPUT "[HARDWARE_FAULT] Module: " + systemModule +
           " -> Error Code: 0x" + hexCode
END FUNCTION


MAIN
    // One argument: intended to call the general INFO overload.
    CALL LogEvent("System started")

    // Two arguments: message and severity.
    // Timestamp is supplied by its default value.
    CALL LogEvent("Temperature warning", 2)

    // Three arguments: message, severity and timestamp.
    CALL LogEvent("Controller failure", 3, "14:35:20")

    // Hardware diagnostic overload: integer and module string.
    CALL LogEvent(255, "MOTOR_DRIVER")
END MAIN
```

## 2. Call and default-argument trace

| Call | Selected intended overload | Resolved values | Expected output |
|---|---|---|---|
| `LogEvent("System started")` | Overload 1 | Prefix = INFO | `[INFO] System started` |
| `LogEvent("Temperature warning", 2)` | Overload 2 | Severity = MEDIUM; timestamp = `00:00:00` | `[MEDIUM] @ 00:00:00 : Temperature warning` |
| `LogEvent("Controller failure", 3, "14:35:20")` | Overload 2 | Severity = CRITICAL; timestamp = `14:35:20` | `[CRITICAL] @ 14:35:20 : Controller failure` |
| `LogEvent(255, "MOTOR_DRIVER")` | Overload 3 | Error code = `0xFF`; module = MOTOR_DRIVER | `[HARDWARE_FAULT] Module: MOTOR_DRIVER -> Error Code: 0xFF` |

## 3. Concepts used

- **Function overloading:** Multiple functions use the same name but have different parameter lists.
- **Default arguments:** Omitted optional arguments receive their declared default values.
- **Inline helper:** `FormatPrefix` is a small helper intended for inline expansion and returns a formatted prefix.
- **Scope of the ambiguity:** In standard C++, default arguments do not create a separate overload. Therefore, the one-argument call conflicts with the two-overload design as originally stated. The intended trace is clear, but the signatures need the correction described above before compiling as C++.
