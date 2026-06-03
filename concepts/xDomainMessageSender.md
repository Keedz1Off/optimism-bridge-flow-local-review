# Concept: xDomainMessageSender

## Meaning

`xDomainMessageSender()` returns the original sender from the other chain.

In simple words:

```text
msg.sender tells you who called locally.
xDomainMessageSender tells you who sent the cross-chain message.
```

## Why It Matters

In bridge finalization, checking only `msg.sender` is not enough.

Usually:

```text
msg.sender = local messenger
xDomainMessageSender = remote bridge
```

The bridge must verify both.

## Main Invariant

```text
The original cross-chain sender must be the trusted counterpart bridge.
```

## If Broken

An attacker may create a spoofed message.

Consequence:

```text
fake mint
fake release
unbacked liquidity
```
