# Withdrawal Break Think

## Flow

```text
withdraw(...)
-> burn tokens on L2
-> encode withdrawal message
-> prove / validate message
-> finalizeWithdrawal(...)
-> release tokens on L1
```

## Invariant 1

```text
L2 burned amount = L1 released amount
```

How can it break:

```text
The release message encodes more than the real burned amount.
```

Consequence:

```text
fake release / bridge insolvency
```

## Invariant 2

```text
Only authentic withdrawal messages can release L1 tokens.
```

How can it break:

```text
finalizeWithdrawal(...) accepts fake messenger/origin data.
```

Consequence:

```text
stolen escrowed funds
```

## Invariant 3

```text
The L2 token must map to the correct L1 token.
```

How can it break:

```text
Token mapping validation is missing or incorrect.
```

Consequence:

```text
burn cheap/wrong token, release valuable L1 token
```
