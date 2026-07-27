# Optimism Bridge Flow Local Review

This repository is an educational local review of an Optimism-style bridge flow.

The goal is to practice bridge security thinking:

```text
Understand the flow -> Define invariants -> Search for violations
```

This is not a full production audit. It is a study repository focused on bridge
architecture, function-by-function review, message passing, replay protection,
auth boundaries, and accounting invariants.

Function code snippets are taken from the official Optimism Bedrock contracts:

```text
ethereum-optimism/optimism/packages/contracts-bedrock/src/
```

This repository follows the Optimism bridge/message flow, not Sky DSS accounting.

## Bridge Model

### Deposit Flow: L1 -> L2

```mermaid
flowchart TD
    A["User on L1"] --> B["bridgeERC20(...) / bridgeERC20To(...)"]
    B --> C["_initiateBridgeERC20(...)"]
    C --> D["safeTransferFrom(user, L1 bridge, amount)"]
    D --> E["deposits[L1Token][L2Token] += amount"]
    E --> F["messenger.sendMessage(...)"]
    F --> G["CrossDomainMessenger sends encoded relayMessage(...)"]
    G --> H["relayMessage(...) on L2"]
    H --> I["finalizeBridgeERC20(...) on L2"]
    I --> J["mint(to, amount)"]
```

Main deposit invariant:

```text
L1 locked amount = L2 minted amount
```

### Withdrawal Flow: L2 -> L1

```mermaid
flowchart TD
    A["User on L2"] --> B["bridgeERC20(...) / bridgeERC20To(...)"]
    B --> C["_initiateBridgeERC20(...)"]
    C --> D["burn(user, amount)"]
    D --> E["messenger.sendMessage(...)"]
    E --> F["CrossDomainMessenger sends encoded relayMessage(...)"]
    F --> G["prove / validate withdrawal on L1"]
    G --> H["relayMessage(...) on L1"]
    H --> I["finalizeBridgeERC20(...) on L1"]
    I --> J["deposits[L1Token][L2Token] -= amount"]
    J --> K["safeTransfer(user, amount)"]
```

Main withdrawal invariant:

```text
L2 burned amount = L1 released amount
```

## Core Functions Reviewed

This repository focuses on the functions that carry the main bridge logic.

### Main Deposit Functions

```text
_initiateBridgeERC20(...)
sendMessage(...)
relayMessage(...)
finalizeBridgeERC20(...)
```

### Main Withdrawal Functions

```text
_initiateBridgeERC20(...)
burn(...) branch
sendMessage(...)
finalizeBridgeERC20(...)
```

### Why These Functions Matter

```text
_initiateBridgeERC20(...) = source-chain accounting and message creation
sendMessage(...) = messenger message creation
relayMessage(...) = message validation, replay protection, and execution
finalizeBridgeERC20(...) = destination-chain mint or release
```

Important Optimism detail:

```text
StandardBridge._initiateBridgeERC20(...) is shared by L1 and L2 bridges.

On L1 deposit:
canonical token -> safeTransferFrom(...) -> deposits += amount

On L2 withdrawal:
OptimismMintableERC20 -> burn(...)
```

## Repository Structure

```text
optimism-bridge-flow-local-review/
+-- README.md
+-- glossary.md
+-- deposit-flow/
|   +-- 01-initiateBridgeERC20.md
|   +-- 02-sendMessage.md
|   +-- 03-relayMessage.md
|   +-- 04-finalizeBridgeERC20.md
+-- withdrawal-flow/
|   +-- 01-initiateBridgeERC20.md
|   +-- 02-burn.md
|   +-- 03-finalizeBridgeERC20.md
+-- break-think/
    +-- README.md
    +-- deposit-break-think.md
    +-- withdrawal-break-think.md
+-- exploit-labs/
    +-- README.md
```

## Exploit Labs

The `exploit-labs/` folder contains simplified PoC-style notes for Optimism bridge security patterns.

The goal is to prove broken invariants with small vulnerable/fixed examples:

```text
missing messenger check
missing counterpart bridge check
withdrawal message created without burn
```

## Glossary

The `glossary.md` file contains important Optimism bridge terms used in this review.

## Global Invariants

### Main Global Invariants

```text
L1 locked amount = L2 minted amount
```

```text
L2 burned amount = L1 released amount
```

```text
Only authentic bridge messages can mint or release tokens.
```

### Additional Deposit Invariants

```text
The L1 token must map to the correct L2 token.
```

```text
The recipient encoded in the message must be the intended recipient.
```

```text
The deposit message must be sent to the trusted counterpart bridge.
```

```text
The deposit message must be finalized only through an authentic messenger path.
```

```text
The same deposit message must not be executed twice.
```

### Additional Withdrawal Invariants

```text
The L2 token must map to the correct L1 token.
```

```text
The withdrawal recipient must be the intended recipient.
```

```text
The withdrawal message must be created only after the burn step.
```

```text
The withdrawal message must be finalized only through an authentic messenger path.
```

```text
The same withdrawal message must not be executed twice.
```

### Additional Messenger Invariants

```text
Only the trusted messenger can relay messages.
```

```text
The message hash must uniquely identify the sender, target, and calldata.
```

```text
The message must be marked as executed before the external target call.
```

```text
Validation must happen before execution.
```

```text
The target must be the intended destination contract.
```

## What I Practiced

- Deposit flow analysis
- Withdrawal flow analysis
- Message authenticity
- Replay protection
- `xDomainMessageSender`
- Ghost mint risk
- Fake release risk
- Accounting invariants
- Token conservation across chains

## Core Security Idea

```text
Real state transition
-> message creation
-> message validation
-> execution
-> mint / release
```

If this relationship breaks, the message stops being proof of real state.

That can lead to:

```text
ghost mint
fake release
replay
double spend
unbacked liquidity
broken bridge accounting
```
