# depositERC20(...)

## Invariants 
1.Transfer must succeed before the bridge sends the message.

2.Message amount must equal the amount actually transferred.

3.The L1 token must match to the correct L2 token.

## Concenquences

1. It may lead to sending a message without locking tokens on L1.

2. The message amount more than the amount actually transfered

<img width="1258" height="430" alt="image" src="https://github.com/user-attachments/assets/a4e08003-fc77-4c08-ac14-8c41ec83d99a" />


3. The token on L1 does not match the token on L2

<img width="1024" height="517" alt="image" src="https://github.com/user-attachments/assets/6394f956-c7d3-4b4b-afba-f76ff5d89a1f" />


# replayMessage()


1. Only the trusted messenger can relay messages.

2. Each message must execute only once.

3. Validation must happen before execution.

## Concenquences

1. Function can be called by anyone.Anyone changes parameter _target, _sender;

2. It may lead to replay (or we can call it as twice exctution)

3. Validion happens after execution that makes validation useless.
 



# finalizeBridgeERC20()

1. 


##


