# Question 3 – Student Academic Roster Using an Array of Objects

## 1. Structured pseudocode

```text
CLASS StudentRecord
    PRIVATE:
        rollNo : INTEGER
        studentName : STRING
        marks : ARRAY[0..2] OF REAL
        percentage : REAL
        grade : CHARACTER

        METHOD CalculateResult()
            total <- marks[0] + marks[1] + marks[2]
            percentage <- total / 3.0

            IF percentage >= 85 THEN
                grade <- 'A'
            ELSE IF percentage >= 70 THEN
                grade <- 'B'
            ELSE IF percentage >= 50 THEN
                grade <- 'C'
            ELSE
                grade <- 'F'
            END IF
        END METHOD

    PUBLIC:
        CONSTRUCTOR StudentRecord()
            rollNo <- 0
            studentName <- ""
            marks[0] <- 0
            marks[1] <- 0
            marks[2] <- 0
            percentage <- 0.0
            grade <- 'F'
        END CONSTRUCTOR

        METHOD ReadData(r, name, m1, m2, m3)
            rollNo <- r
            studentName <- name
            marks[0] <- m1
            marks[1] <- m2
            marks[2] <- m3
            CALL CalculateResult()
        END METHOD

        METHOD GetPercentage() RETURNS REAL
            RETURN percentage
        END METHOD

        METHOD Display()
            OUTPUT rollNo, studentName, marks[0], marks[1], marks[2],
                   percentage, grade
        END METHOD
END CLASS


MAIN
    DECLARE students : ARRAY[0..2] OF StudentRecord
    DECLARE i, pass : INTEGER
    DECLARE temp : StudentRecord

    // Populate three objects with sample data.
    students[0].ReadData(101, "Anu", 80, 90, 70)
    students[1].ReadData(102, "Bala", 60, 70, 80)
    students[2].ReadData(103, "Charu", 90, 95, 85)

    // Bubble Sort in descending order of percentage.
    FOR pass <- 0 TO 1
        FOR i <- 0 TO 1 - pass
            IF students[i].GetPercentage() <
               students[i + 1].GetPercentage() THEN

                temp <- students[i]
                students[i] <- students[i + 1]
                students[i + 1] <- temp
            END IF
        END FOR
    END FOR

    OUTPUT "Rank  Roll No  Name  Percentage  Grade"
    FOR i <- 0 TO 2
        OUTPUT i + 1
        students[i].Display()
    END FOR
END MAIN
```

## 2. Sample percentages

| Roll No. | Name | Marks | Total | Percentage | Grade |
|---:|---|---|---:|---:|:---:|
| 101 | Anu | 80, 90, 70 | 240 | 80.00% | B |
| 102 | Bala | 60, 70, 80 | 210 | 70.00% | B |
| 103 | Charu | 90, 95, 85 | 270 | 90.00% | A |

## 3. Bubble Sort dry-run trace

Initial order: **Anu (80), Bala (70), Charu (90)**

| Pass | Comparison | Condition | Action | Order after comparison |
|---:|---|---|---|---|
| 1 | Anu 80 vs Bala 70 | 80 < 70 is false | No swap | Anu, Bala, Charu |
| 1 | Bala 70 vs Charu 90 | 70 < 90 is true | Swap | Anu, Charu, Bala |
| 2 | Anu 80 vs Charu 90 | 80 < 90 is true | Swap | Charu, Anu, Bala |

**Final rank list**

| Rank | Roll No. | Name | Percentage | Grade |
|---:|---:|---|---:|:---:|
| 1 | 103 | Charu | 90.00% | A |
| 2 | 101 | Anu | 80.00% | B |
| 3 | 102 | Bala | 70.00% | B |

Bubble Sort compares adjacent objects and swaps them when the left object's percentage is lower. The array is therefore arranged in descending order.
