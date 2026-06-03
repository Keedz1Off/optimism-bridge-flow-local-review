# Concept: Message Hash and Replay Protection

## Message Hash

Example:

```solidity
bytes32 hash = keccak256(
    abi.encode(
        _sender,
        _target,
        _message
    )
);
```

This creates a unique fingerprint for the message.

## Why It Matters

The bridge needs to know:

```text
Was this exact message already executed?
```

If yes, it must not execute again.

## Replay Protection

Example:

```solidity
require(!successfulMessages[hash]);
successfulMessages[hash] = true;
```

This prevents the same message from executing twice.

## Main Invariant

```text
One valid message = one execution.
```

## If Broken

Example:

```text
mint(100)
mint(100)
mint(100)
```

Consequence:

```text
replay attack / infinite mint / double release
```
