# _initiateBridgeERC20(...)

## Invariants 
1.Transfer must succeed before the bridge sends the message.

2.Message amount must equal the amount actually transferred.

3.The L1 token must match to the correct L2 token.

## Consequences

1. It may lead to sending a message without locking tokens on L1.

2. The message amount may be greater than the amount actually transferred.

<img width="1258" height="430" alt="image" src="https://github.com/user-attachments/assets/a4e08003-fc77-4c08-ac14-8c41ec83d99a" />


3. The token on L1 does not match the token on L2

<img width="1024" height="517" alt="image" src="https://github.com/user-attachments/assets/6394f956-c7d3-4b4b-afba-f76ff5d89a1f" />


# relayMessage()

## Invariants

1. Only the trusted messenger can relay messages.

2. Each message must execute only once.

3. Validation must happen before execution.

## Consequences

1. The function can be called by anyone. An attacker may change the _target or _sender parameters.

2. It may lead to replay or double execution.

3. Validation happens after execution, which makes validation useless.
 



# finalizeBridgeERC20()

## Invariants
1. Only the messenger can call finalizeBridgeERC20(...).

2. The original cross-chain sender must be the trusted counterpart bridge.

## Consequences
1. This may be called by anyone, which can lead to ghost mint (mint without lock).

2. In the worst case, this function can be called by another spoofed bridge instead of the original L1 bridge.












