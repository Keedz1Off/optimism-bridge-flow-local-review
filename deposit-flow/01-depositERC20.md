# Function Review: depositERC20(...)

## 1. Function Code

```solidity
function depositERC20(
    address l1Token,
    address l2Token,
    uint256 amount,
    address to
) external {
    IERC20(l1Token).transferFrom(
        msg.sender,
        address(this),
        amount
    );

    bytes memory message = abi.encodeCall(
        finalizeBridgeERC20,
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
This is simplified Optimism-style bridge code for learning.
The exact production implementation can have more checks and parameters.
```

## 2. What This Function Does

`depositERC20(...)` starts the L1 -> L2 deposit flow.

In simple words:

```text
The user sends tokens to the L1 bridge.
The bridge creates a message.
The message later tells the L2 bridge to mint / credit tokens.
```

This function connects:

- user token transfer
- L1 lock / escrow
- calldata creation
- cross-chain message sending
- later L2 minting

## 3. Important Parts Explained

### Token Transfer

```solidity
IERC20(l1Token).transferFrom(
    msg.sender,
    address(this),
    amount
);
```

This moves tokens from the user to the bridge contract.

Meaning:

```text
msg.sender -> bridge contract
```

Security meaning:

```text
This is the source-chain accounting step.
```

The bridge must not send a mint message unless the L1 token transfer really
happened.

### Message Encoding

```solidity
bytes memory message = abi.encodeCall(
    finalizeBridgeERC20,
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

This builds the calldata that will be executed on the destination chain.

Important values inside the message:

- `l1Token`
- `l2Token`
- `from`
- `to`
- `amount`

Security meaning:

```text
The message must represent what really happened on L1.
```

If the encoded `amount` is wrong, L2 can mint the wrong amount.

### Send Message

```solidity
messenger.sendMessage(
    address(otherBridge),
    message
);
```

This sends the encoded message to the counterpart bridge.

Security meaning:

```text
The target must be the trusted bridge on the other chain.
```

## 4. Main Invariants

### Invariant 1

```text
Transfer must succeed before the bridge sends the message.
```

### Invariant 2

```text
Message amount must equal the amount actually transferred.
```

### Invariant 3

```text
The L1 token must map to the correct L2 token.
```

## 5. Short Conclusion

`depositERC20(...)` is the start of the deposit flow.

The most important question:

```text
Does the L2 message represent the real L1 token transfer?
```
