# Function Review: finalizeWithdrawal(...)

## 1. Function Code

```solidity
function finalizeWithdrawal(
    address l1Token,
    address l2Token,
    address from,
    address to,
    uint256 amount,
    bytes calldata extraData
) external {
    require(
        msg.sender == address(messenger)
    );

    require(
        messenger.xDomainMessageSender() == address(otherBridge)
    );

    IERC20(l1Token).transfer(
        to,
        amount
    );
}
```

Note:

```text
This is simplified Optimism-style withdrawal finalization code for learning.
```

## 2. What This Function Does

`finalizeWithdrawal(...)` completes the L2 -> L1 withdrawal.

In simple words:

```text
The L1 bridge receives a valid withdrawal message and releases escrowed tokens.
```

## 3. Important Parts Explained

### Messenger Check

```solidity
require(
    msg.sender == address(messenger)
);
```

The direct caller must be the L1 messenger.

Security meaning:

```text
Users should not directly finalize withdrawals.
```

### Original Bridge Check

```solidity
require(
    messenger.xDomainMessageSender() == address(otherBridge)
);
```

The original sender on L2 must be the trusted L2 bridge.

Security meaning:

```text
The message must come from the real counterpart bridge.
```

### Release Tokens

```solidity
IERC20(l1Token).transfer(
    to,
    amount
);
```

This releases L1 tokens to the recipient.

Security meaning:

```text
This release must be backed by a real L2 burn.
```

## 4. Main Invariants

### Invariant 1

```text
Only an authentic withdrawal message can release L1 tokens.
```

### Invariant 2

```text
Released amount must equal burned amount.
```

### Invariant 3

```text
Released token must be the correct L1 token for the burned L2 token.
```

## 5. Short Conclusion

`finalizeWithdrawal(...)` is the L1 release boundary.

The most important question:

```text
Is this release backed by a valid L2 burn message?
```
