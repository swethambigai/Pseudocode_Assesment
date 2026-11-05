# Question 1 – Corporate Vault Management with Static Data and Functions

## 1. Structured pseudocode

```text
CLASS BranchOffice
    PRIVATE:
        branchID : STRING
        branchName : STRING
        currentBalance : REAL

        STATIC totalVaultReserves : REAL = 0.0
        STATIC totalBranchesActive : INTEGER = 0

    PUBLIC:

        CONSTRUCTOR BranchOffice(id, name, openingDeposit)
            branchID <- id
            branchName <- name
            currentBalance <- openingDeposit

            totalVaultReserves <- totalVaultReserves + openingDeposit
            totalBranchesActive <- totalBranchesActive + 1
        END CONSTRUCTOR

        METHOD Deposit(amount : REAL)
            IF amount > 0 THEN
                currentBalance <- currentBalance + amount
                totalVaultReserves <- totalVaultReserves + amount
                OUTPUT "Deposit successful"
            ELSE
                OUTPUT "Invalid deposit amount"
            END IF
        END METHOD

        METHOD Withdraw(amount : REAL) RETURNS BOOLEAN
            IF amount > 0 AND currentBalance >= amount THEN
                currentBalance <- currentBalance - amount
                totalVaultReserves <- totalVaultReserves - amount
                RETURN TRUE
            ELSE
                RETURN FALSE
            END IF
        END METHOD

        STATIC METHOD GetVaultSummary()
            OUTPUT "Active branches: ", totalBranchesActive
            OUTPUT "Total vault reserves: ", totalVaultReserves
        END METHOD

        STATIC METHOD GetGlobalLiquidityRatio(targetReserve : REAL) RETURNS REAL
            IF targetReserve <= 0 THEN
                OUTPUT "Target reserve must be positive"
                RETURN 0.0
            END IF

            RETURN totalVaultReserves / targetReserve
        END METHOD
END CLASS


MAIN
    DECLARE Branch1, Branch2 : BranchOffice

    Branch1 <- NEW BranchOffice("B001", "Central Branch", 500000.0)
    Branch2 <- NEW BranchOffice("B002", "City Branch", 300000.0)

    OUTPUT "Initial vault summary"
    BranchOffice::GetVaultSummary()

    Branch1.Deposit(150000.0)

    IF Branch2.Withdraw(50000.0) = TRUE THEN
        OUTPUT "Branch2 withdrawal successful"
    ELSE
        OUTPUT "Branch2 withdrawal rejected"
    END IF

    OUTPUT "Final vault summary"
    BranchOffice::GetVaultSummary()

    ratio <- BranchOffice::GetGlobalLiquidityRatio(1000000.0)
    OUTPUT "Global liquidity ratio: ", ratio
END MAIN
```

## 2. Trace table

The question does not specify opening deposits. For this trace, Branch1 starts with $500,000 and Branch2 starts with $300,000. The target reserve used in the driver is $1,000,000.

| Step | Operation | Branch1 balance | Branch2 balance | Active branches | Static totalVaultReserves |
|---|---|---:|---:|---:|---:|
| 0 | Initial state before object creation | — | — | 0 | $0 |
| 1 | Create Branch1 with $500,000 | $500,000 | — | 1 | $500,000 |
| 2 | Create Branch2 with $300,000 | $500,000 | $300,000 | 2 | $800,000 |
| 3 | Branch1 deposits $150,000 | $650,000 | $300,000 | 2 | $950,000 |
| 4 | Branch2 withdraws $50,000 | $650,000 | $250,000 | 2 | $900,000 |

**Final results**

- Total active branches = 2
- Total vault reserves = $900,000
- Liquidity ratio = $900,000 / $1,000,000 = **0.90**

The static reserve is shared by all BranchOffice objects. Each successful transaction updates both the individual branch balance and the shared reserve.
