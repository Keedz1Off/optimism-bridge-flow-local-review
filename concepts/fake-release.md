# Concept: Fake Release

## Meaning

Fake release means:

```text
L1 tokens are released without a valid L2 burn proof/message
```

## Example

```text
Attacker keeps 100 tokens on L2
Attacker triggers fake L1 release
Attacker receives 100 tokens on L1
```

Result:

```text
100 still exists on L2
100 appears on L1
```

This duplicates value.

## Main Invariant

```text
L1 release must be backed by a real L2 burn.
```

## Consequence

```text
double spend
fund loss
bridge insolvency
state desync
```
