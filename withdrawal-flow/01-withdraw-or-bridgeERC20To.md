# Function Review: withdraw(...) / bridgeERC20To(...)

## 1. Function Code

```solidity
function withdraw(
    address l2Token,
    address l1Token,
    uint256 amount,
    address to
) external {
    IOptimismMintableERC20(l2Token).burn(
        msg.sender,
        amount
    );

    bytes memory message = abi.encodeCall(
        finalizeWithdrawal,
        (
            l1Token,
            l2Token,
            msg.sender,
            to,
            amount,
            ""
        )
    );

    messenger.sendMessage(
        address(otherBridge),
        message
    );
}
```

Note:

```text
This is simplified Optimism-style withdrawal code for learning.
```

## 2. What This Function Does

This function starts the L2 -> L1 withdrawal flow.

In simple words:

```text
The user burns tokens on L2.
The bridge creates a message.
The message later tells the L1 bridge to release tokens.
```

## 3. Important Parts Explained

### Burn on L2

```solidity
IOptimismMintableERC20(l2Token).burn(
    msg.sender,
    amount
);
```

This removes tokens from the user's L2 balance.

Security meaning:

```text
L1 release must be backed by a real L2 burn.
```

### Withdrawal Message

```solidity
bytes memory message = abi.encodeCall(
    finalizeWithdrawal,
    (
        l1Token,
        l2Token,
        msg.sender,
        to,
        amount,
        ""
    )
);
```

This creates the message that will later release tokens on L1.

Security meaning:

```text
The release message must match the real burn.
```

## 4. Main Invariants

### Invariant 1

```text
Tokens must be burned on L2 before the L1 release message is finalized.
```

If broken:

```text
The user may keep L2 tokens and also receive L1 tokens.
```

Consequence:

```text
double spend / fake release
```

### Invariant 2

```text
Burn amount must equal release amount.
```

If broken:

```text
The bridge may release more tokens than were burned.
```

Consequence:

```text
bridge insolvency / broken accounting
```

### Invariant 3

```text
The L2 token must map to the correct L1 token.
```

If broken:

```text
The user may burn a wrong or cheap token and release a valuable L1 token.
```

Consequence:

```text
token mapping exploit
```

## 5. Short Conclusion

Withdrawal starts with a source-chain burn.

The most important question:

```text
Does the L1 release represent a real L2 burn?
```
