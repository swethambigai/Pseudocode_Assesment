# Question 5 – Hardware Register Bank with Return by Reference

## 1. Structured pseudocode

```text
CLASS RegisterBank
    PRIVATE:
        registers : ARRAY[0..7] OF INTEGER
        errorSink : INTEGER

    PUBLIC:
        CONSTRUCTOR RegisterBank()
            FOR i <- 0 TO 7
                registers[i] <- 0
            END FOR

            errorSink <- -1
        END CONSTRUCTOR


        METHOD GetRegister(index : INTEGER) RETURNS REF INTEGER
            IF index >= 0 AND index < 8 THEN
                RETURN REFERENCE TO registers[index]
            ELSE
                OUTPUT "Alert: register index out of bounds"
                RETURN REFERENCE TO errorSink
            END IF
        END METHOD


        METHOD DisplayState()
            OUTPUT "Register Bank State (Hexadecimal)"
            FOR i <- 0 TO 7
                OUTPUT "Register[", i, "] = 0x",
                       ConvertIntegerToHexadecimal(registers[i])
            END FOR
        END METHOD
END CLASS


MAIN
    DECLARE bank : RegisterBank
    DECLARE readVal : INTEGER

    bank <- NEW RegisterBank()

    // Direct lvalue assignment through returned references.
    bank.GetRegister(0) <- 170
    bank.GetRegister(1) <- 255

    // Read the value stored in register 0.
    readVal <- bank.GetRegister(0)
    OUTPUT "Value read from register 0: ", readVal

    // Invalid index: the returned reference points to errorSink.
    bank.GetRegister(10) <- 999

    OUTPUT "Register bank after operations:"
    bank.DisplayState()
END MAIN
```

## 2. Expected state trace

| Operation | Effect on registers | errorSink |
|---|---|---:|
| Constructor | All eight registers become 0 | -1 |
| `GetRegister(0) <- 170` | Register 0 becomes 170 | -1 |
| `GetRegister(1) <- 255` | Register 1 becomes 255 | -1 |
| `readVal <- GetRegister(0)` | No register changes; `readVal` becomes 170 | -1 |
| `GetRegister(10) <- 999` | No valid register changes; assignment targets errorSink | 999 |

Expected register values in hexadecimal:

| Register | Decimal value | Hexadecimal |
|---:|---:|---:|
| 0 | 170 | 0xAA |
| 1 | 255 | 0xFF |
| 2–7 | 0 | 0x00 |

The invalid-index operation emits an alert and changes `errorSink` to 999. It does not modify any of the eight valid registers.

## 3. Explanation of return by reference

A function that returns by reference gives the caller an alias to an existing variable rather than returning only a separate copy of its value.

For a valid index, `GetRegister(index)` returns a reference to `registers[index]`. Therefore:

```text
bank.GetRegister(0) <- 170
```

acts like assigning 170 directly to `registers[0]`. The function call can appear on the left side because the returned reference identifies the original storage location and behaves as an assignable lvalue.

For an invalid index, the method returns a reference to `errorSink`. This prevents the invalid operation from accessing an element outside the array. The assignment changes the error sink instead of the register array.

**Important:** The returned reference must refer to an object that remains alive after the function returns. Returning a reference to a local temporary variable would be unsafe. In this design, both `registers` and `errorSink` are members of the RegisterBank object, so they remain available while that object exists.
