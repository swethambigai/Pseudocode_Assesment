# Question 2 – Matrix Mathematical Engine with Friend Dot-Product

## 1. Class declaration and implementations

```text
CLASS Matrix2D
    PRIVATE:
        grid : ARRAY[0..2][0..2] OF REAL

    PUBLIC:
        METHOD Initialize()
        METHOD SetElement(row : INTEGER, col : INTEGER, value : REAL)
        METHOD GetElement(row : INTEGER, col : INTEGER) RETURNS REAL
        METHOD Display()

        FRIEND FUNCTION ComputeCrossTrace(A : Matrix2D, B : Matrix2D)
            RETURNS REAL
END CLASS


// Member function definitions outside the class.
// The scope-resolution operator identifies the class to which each method belongs.

METHOD Matrix2D::Initialize()
    FOR row <- 0 TO 2
        FOR col <- 0 TO 2
            grid[row][col] <- 0.0
        END FOR
    END FOR
END METHOD


METHOD Matrix2D::SetElement(row, col, value)
    IF row >= 0 AND row < 3 AND col >= 0 AND col < 3 THEN
        grid[row][col] <- value
    ELSE
        OUTPUT "Error: matrix index out of bounds"
    END IF
END METHOD


METHOD Matrix2D::GetElement(row, col) RETURNS REAL
    IF row >= 0 AND row < 3 AND col >= 0 AND col < 3 THEN
        RETURN grid[row][col]
    ELSE
        OUTPUT "Error: matrix index out of bounds"
        RETURN 0.0
    END IF
END METHOD


METHOD Matrix2D::Display()
    FOR row <- 0 TO 2
        FOR col <- 0 TO 2
            OUTPUT grid[row][col], " "
        END FOR
        OUTPUT NEWLINE
    END FOR
END METHOD


// This is an external friend function, not a Matrix2D member method.
// Friendship permits direct access to the private grid arrays.

FUNCTION ComputeCrossTrace(A : Matrix2D, B : Matrix2D) RETURNS REAL
    sum <- 0.0

    FOR i <- 0 TO 2
        sum <- sum + (A.grid[i][i] * B.grid[i][i])
    END FOR

    RETURN sum
END FUNCTION


MAIN
    DECLARE A, B : Matrix2D

    A.Initialize()
    B.Initialize()

    // Sample matrix A
    A.SetElement(0, 0, 2)
    A.SetElement(1, 1, 4)
    A.SetElement(2, 2, 6)

    // Sample matrix B
    B.SetElement(0, 0, 1)
    B.SetElement(1, 1, 3)
    B.SetElement(2, 2, 5)

    OUTPUT "Matrix A:"
    A.Display()

    OUTPUT "Matrix B:"
    B.Display()

    result <- ComputeCrossTrace(A, B)
    OUTPUT "Cross trace dot product: ", result
END MAIN
```

## 2. Friend function dry run

Given:

- A diagonal = [2, 4, 6]
- B diagonal = [1, 3, 5]

| Iteration | A.grid[i][i] | B.grid[i][i] | Product | Running sum |
|---:|---:|---:|---:|---:|
| Initial | — | — | — | 0 |
| i = 0 | 2 | 1 | 2 × 1 = 2 | 2 |
| i = 1 | 4 | 3 | 4 × 3 = 12 | 14 |
| i = 2 | 6 | 5 | 6 × 5 = 30 | 44 |

**Final result: 44**

The friend function can access the private `grid` arrays directly because it is declared as a friend inside the Matrix2D class. The `Matrix2D::` scope-resolution notation identifies an implementation as belonging to the Matrix2D class.
