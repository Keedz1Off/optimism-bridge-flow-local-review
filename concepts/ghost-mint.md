# Concept: Ghost Mint

## Meaning

Ghost mint means:

```text
tokens are minted without a real lock or burn on the source chain
```

## Example

```text
L1 locked: 0
L2 minted: 100
```

This is broken because destination-chain value was created without backing.

## When It Can Happen

- message created without transfer
- transfer failed but message was still sent
- false-return token ignored
- amount encoded from user input instead of actual received amount
- fake message accepted
- auth boundary broken

## Main Invariant

```text
Minted amount must be backed by real locked / burned amount.
```

## Consequence

```text
unbacked liquidity
inflation
bridge insolvency
broken accounting
```
