# Glossary

This file contains important words used in this Optimism bridge review.

The goal is to keep the key audit vocabulary in one place.

## Core Terms

| Term | Simple Meaning | Where It Matters |
|---|---|---|
| bridge | System that moves value/messages between chains | Full deposit and withdrawal flow |
| L1 | Ethereum / source settlement chain | L1 Standard Bridge |
| L2 | Optimism-style chain | L2 Standard Bridge |
| StandardBridge | Main bridge contract pattern | `_initiateBridgeERC20(...)`, `finalizeBridgeERC20(...)` |
| messenger | CrossDomainMessenger contract | `sendMessage(...)`, `relayMessage(...)` |
| counterpart bridge | Expected bridge on the other chain | `onlyOtherBridge` / counterpart checks |
| deposits mapping | Accounting for locked L1 tokens | `deposits[localToken][remoteToken]` |
| lock | Keep L1 tokens inside the bridge | L1 deposit |
| release | Send locked L1 tokens back to the user | L1 withdrawal finalization |
| burn | Destroy L2 tokens before withdrawal | L2 withdrawal |
| mint | Create L2 tokens after deposit finalization | L2 deposit finalization |
| relay message | Cross-chain message execution through messenger | `relayMessage(...)` |
| xDomainMessageSender | Original sender from the other chain | Counterpart validation |
| calldata | Encoded function call data | `sendMessage(...)`, `relayMessage(...)` |
| payload | Encoded bridge message data | Message passing |
| replay protection | Protection against executing the same message twice | Messenger logic |
| remote token | Token address on the other chain | Token mapping |
| local token | Token address on the current chain | Token mapping |

## Security Words

| Term | Simple Meaning |
|---|---|
| invariant | Rule that must always stay true |
| broken invariant | Rule that was violated |
| impact | What bad thing can happen |
| root cause | Why the bug exists |
| spoofed message | Fake message pretending to be authentic |
| missing messenger check | Finalize function can be called directly |
| missing counterpart check | Real messenger accepts message from fake bridge |
| wrong token mapping | Local token does not match remote token |
| wrong recipient | Funds are minted/released to the wrong address |
| unbacked tokens | Tokens minted/released without real backing |
| stuck funds | Funds are locked/burned but not received |
| finalization | Destination-side execution of the bridge message |

## Simple English Phrases

```text
Only the official messenger can call finalizeBridgeERC20().
Only the trusted counterpart bridge can finalize a message.
L1 locked amount must equal L2 minted amount.
L2 burned amount must equal L1 released amount.
The withdrawal message must be created only after burn.
```
