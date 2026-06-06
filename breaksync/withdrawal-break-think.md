
# bridgeERC20To(...)

## Invariants


1. Tokens must be burned on L2 before the L1 release message is finalized.

2. Burn amount must equal release amount.

3. The L2 token must map to the correct L1 token.


## Concenquences 

1. If tokens were not burned on L2 this may lead to release tokens on L1 without burning on L2.

2. Diffrent amount 
