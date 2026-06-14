# Function Review: sendMessage(...)

## 1. Function Code

```solidity
function sendMessage(address _target, bytes calldata _message, uint32 _minGasLimit) external payable {
    _sendMessage({
        _to: address(otherMessenger),
        _gasLimit: baseGas(_message, _minGasLimit),
        _value: msg.value,
        _data: abi.encodeWithSelector(
            this.relayMessage.selector, messageNonce(), msg.sender, _target, msg.value, _minGasLimit, _message
        )
    });

    emit SentMessage(_target, msg.sender, _message, messageNonce(), _minGasLimit);
    emit SentMessageExtension1(msg.sender, msg.value);

    unchecked {
        ++msgNonce;
    }
}
```

Note:

```text
Original source: ethereum-optimism/optimism/packages/contracts-bedrock/src/universal/CrossDomainMessenger.sol
```

## 2. What This Function Does

`sendMessage(...)` creates the cross-chain message.

In the bridge flow, `StandardBridge._initiateBridgeERC20(...)` calls:

```text
messenger.sendMessage(...)
```

Then the messenger wraps the target calldata into a message that will later call
`relayMessage(...)` on the other chain.

Simple flow:

```text
Bridge calldata
-> sendMessage(...)
-> encoded relayMessage(...)
-> message goes to the other messenger
```

## 3. Important Parts Explained

### Target and Message

```solidity
function sendMessage(address _target, bytes calldata _message, uint32 _minGasLimit) external payable
```

`_target` is the contract that should be called on the other chain.

For ERC20 bridge deposits, this target is usually:

```text
otherBridge
```

`_message` is the calldata that should be executed on the target.

For ERC20 bridge deposits, this calldata usually contains:

```text
finalizeBridgeERC20(...)
```

### Wrap Into relayMessage(...)

```solidity
_data: abi.encodeWithSelector(
    this.relayMessage.selector,
    messageNonce(),
    msg.sender,
    _target,
    msg.value,
    _minGasLimit,
    _message
)
```

This builds the calldata for the remote messenger.

Meaning:

```text
The remote messenger will receive relayMessage(...)
relayMessage(...) will call the target contract
```

### Gas Calculation

```solidity
_gasLimit: baseGas(_message, _minGasLimit)
```

This adds messenger overhead to the user's minimum gas limit.

Simple meaning:

```text
The message needs enough gas to be relayed and executed.
```

### Nonce Update

```solidity
unchecked {
    ++msgNonce;
}
```

This increments the message nonce after sending.

Simple meaning:

```text
Each message should have a unique nonce.
```

