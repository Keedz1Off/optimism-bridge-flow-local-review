# Function Review: relayMessage(...)

## 1. Function Code

```solidity
function relayMessage(
    address _target,
    address _sender,
    bytes memory _message
) external {
    require(
        msg.sender == address(otherMessenger)
    );

    bytes32 hash = keccak256(
        abi.encode(
            _sender,
            _target,
            _message
        )
    );

    require(
        !successfulMessages[hash]
    );

    successfulMessages[hash] = true;

    (bool success,) = _target.call(_message);

    require(success);
}
```

Note:

```text
This is simplified Optimism-style messenger logic for learning.
```

## 2. What This Function Does

`relayMessage(...)` validates and executes a cross-chain message.

In simple words:

```text
It checks that the message came from the trusted messenger,
checks that the message was not already executed,
then calls the target contract.
```

## 3. Important Parts Explained

### Trusted Messenger Check

```solidity
require(
    msg.sender == address(otherMessenger)
);
```

Only the trusted messenger can call this relay function.

Security meaning:

```text
This is the auth boundary.
```

If anyone can call this function directly, they may execute fake bridge messages.

### Message Hash

```solidity
bytes32 hash = keccak256(
    abi.encode(
        _sender,
        _target,
        _message
    )
);
```

This creates a unique message fingerprint.

It includes:

- original sender
- destination target
- message calldata

Security meaning:

```text
The hash identifies this exact message.
```

### Replay Protection

```solidity
require(
    !successfulMessages[hash]
);
```

This checks that the same message was not already executed.

### Mark Executed Before Call

```solidity
successfulMessages[hash] = true;
```

The message is marked as executed before the external call.

Security meaning:

```text
The same message should not be executed twice.
```

### Execute Target

```solidity
(bool success,) = _target.call(_message);

require(success);
```

This calls the destination contract with the provided calldata.

If execution fails, the function reverts.

## 4. Invariants

### Main Invariant 1

```text
Only the trusted messenger can relay messages.
```

### Main Invariant 2

```text
Each message must execute only once.
```

### Main Invariant 3

```text
Validation must happen before execution.
```

## 5. Additional Invariants

### Additional Invariant 1

```text
The message hash must uniquely identify the sender, target, and calldata.
```

### Additional Invariant 2

```text
The message must be marked as executed before the external target call.
```

### Additional Invariant 3

```text
The target must be the intended destination contract.
```

### Additional Invariant 4

```text
The target call must revert if message execution fails.
```
