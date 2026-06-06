# _initiateBridgeERC20(...)

## Invariants

1. Tokens must be burned on L2 before the L1 release message is finalized.

2. Burn amount must equal release amount.

3. The L2 token must map to the correct L1 token.

## Concenquences

1. If tokens were not burned on L2 this may lead to release tokens on L1 without burning on L2.

2. The Bridge releases more tokens than the actually burned tokens.

3. The token on L1 does not match the token on L2.

# finalizeBridgeERC20(...)

## Invariants

1. Only an authentic withdrawal message can release L1 tokens.

2. Released amount must equal burned amount.

3. Released token must be the correct L1 token for the burned L2 token.

## Concenquences

1. An Attacker creates a spoofed message that may lead to a ake release.

2. The Bridge releases more tokens than the actually burned tokens.
   <img width="922" height="261" alt="image" src="https://github.com/user-attachments/assets/414285db-10ee-4760-9bd3-97773a55e011" />
3.  A user receives another token.
   <img width="783" height="339" alt="image" src="https://github.com/user-attachments/assets/f93a08b9-ba18-4670-b247-b803ad6795b6" />

   



