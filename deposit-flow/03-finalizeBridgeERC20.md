# Function Review: finalizeBridgeERC20(...)

## 1. Function Code

```solidity
function finalizeBridgeERC20(
    address localToken,
    address remoteToken,
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

    IOptimismMintableERC20(localToken).mint(
        to,
        amount
    );
}
```

Note:

```text
This is simplified Optimism-style bridge finalization code for learning.
```

## 2. What This Function Does

`finalizeBridgeERC20(...)` completes the deposit on the destination chain.

In simple words:

```text
The L2 bridge receives a valid cross-chain message and mints tokens to the user.
```

This function is critical because it creates destination-chain value.

## 3. Important Parts Explained

### Messenger Check

```solidity
require(
    msg.sender == address(messenger)
);
```

The direct caller must be the local messenger.

Security meaning:

```text
Users should not be able to call finalizeBridgeERC20(...) directly.
```

### Original Sender Check

```solidity
require(
    messenger.xDomainMessageSender() == address(otherBridge)
);
```

This checks who sent the message on the other chain.

Security meaning:

```text
The original sender must be the trusted counterpart bridge.
```

This is stronger than only checking `msg.sender`.

### Mint

```solidity
IOptimismMintableERC20(localToken).mint(
    to,
    amount
);
```

This mints destination-chain tokens to the recipient.

Security meaning:

```text
Minting must be backed by a real lock / burn on the source chain.
```

## 4. Invariants

### Main Invariant 1

```text
Only the messenger can call finalizeBridgeERC20(...).
```

### Main Invariant 2

```text
The original cross-chain sender must be the trusted counterpart bridge.
```

### Main Invariant 3

```text
Mint amount must equal the real source-chain locked amount.
```

## 5. Additional Invariants

### Additional Invariant 1

```text
The minted token must be the correct local token for the remote token.
```

### Additional Invariant 2

```text
The recipient must be the recipient encoded in the authentic bridge message.
```

### Additional Invariant 3

```text
The function must not accept direct user calls.
```

### Additional Invariant 4

```text
The finalize calldata must not change token, recipient, or amount during execution.
```
