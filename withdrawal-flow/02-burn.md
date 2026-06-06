# Function Review: burn(...)

## 1. Function Code

```solidity
function burn(
    address from,
    uint256 amount
) external onlyBridge {
    _burn(from, amount);
}
```

Note:

```text
This is simplified token burn logic for learning.
```

## 2. What This Function Does

`burn(...)` removes tokens from a user's balance.

In withdrawal flow, this is the source-chain state transition.

In simple words:

```text
Before the user receives tokens on L1, their L2 tokens must be removed.
```

## 3. Important Parts Explained

### Bridge Authorization

```solidity
external onlyBridge
```

Only the bridge should be able to call this burn function.

Security meaning:

```text
Random users should not be able to burn other users' tokens.
```

### Burn State Change

```solidity
_burn(from, amount);
```

This decreases:

- the user's token balance
- total token supply

Security meaning:

```text
This is the economic proof for the later L1 release.
```

## 4. Main Invariants

### Invariant 1

```text
Only the authorized bridge can burn bridge tokens.
```

### Invariant 2

```text
Burned amount must be the amount encoded for withdrawal.
```

### Invariant 3

```text
The user must have enough balance to burn the requested amount.
```

### Invariant 4

```text
The burn must reduce the user's balance and total supply by the same amount.
```

### Invariant 5

```text
The burn amount should be greater than zero.
```
