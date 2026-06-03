# Deposit Break Think

## Flow

```text
depositERC20(...)
-> lock tokens on L1
-> encode message
-> sendMessage(...)
-> relayMessage(...)
-> finalizeBridgeERC20(...)
-> mint on L2
```

## Invariant 1

```text
L1 locked amount = L2 minted amount
```

How can it break:

```text
The bridge trusts user-supplied amount instead of actual transferred amount.
```

Consequence:

```text
ghost mint / unbacked L2 tokens
```

## Invariant 2

```text
Only authentic bridge messages can mint tokens.
```

How can it break:

```text
finalizeBridgeERC20(...) accepts a fake sender or weak messenger auth.
```

Consequence:

```text
fake mint / arbitrary mint
```

## Invariant 3

```text
One message can execute only once.
```

How can it break:

```text
Replay protection is missing or message hash is weak.
```

Consequence:

```text
same deposit mints multiple times
```
