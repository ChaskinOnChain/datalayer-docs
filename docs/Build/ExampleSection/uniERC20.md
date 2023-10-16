---
id: example-uniERC20
title: Universal ERC20
sidebar_position: 1
---

Universal ERC20 extends the ERC20 token standard by integrating cross-chain transfer capabilities through SocketDL. It revolutionizes the concept of token interoperability across multiple blockchain networks. With Universal ERC20, tokens are burnt on the source chain and minted afresh on the destination chain, ensuring a seamless, secure, and efficient transfer process.

### Key Features

- **Cross-Chain Transfers:** Universal ERC20 tokens can be transferred between different blockchain networks.
- **Burn & Mint Mechanism:** Tokens are burnt on the source chain and minted on the destination chain to conserve the total supply.
- **Integrated with SocketDL:** Leverages the power and security of SocketDL for cross-chain interactions.

Explore the Universal ERC20 implementation on [GitHub](https://github.com/SocketDotTech/socketDL-examples/blob/main/src/universalTokens/uniERC20/uniERC20.sol).

## Understanding the Code

### `uniTransfer` Function

The `uniTransfer` function is pivotal for initiating cross-chain transfers. It burns the specified amount of tokens from the sender's balance on the source chain. Concurrently, it triggers an outbound message to the destination chain to mint an equivalent amount of tokens.

Here’s a breakdown of the steps:

1. **Token Burning:** The `_burn` function is called to burn tokens from the sender’s balance.
2. **Message Encoding:** The sender's address, recipient's address, and the amount to be transferred are encoded into a payload.
3. **Outbound Message:** An outbound message, carrying the encoded payload, is sent to the destination chain through SocketDL.

### `_uniReceive` Function

The `_uniReceive` function, called upon the receipt of the inbound message on the destination chain, decodes the payload and mints the equivalent amount of tokens that were burnt on the source chain.

Here’s a simplified explanation:

1. **Payload Decoding:** Decode the sender’s address, recipient’s address, and the amount from the received payload.
2. **Token Minting:** Mint tokens to the recipient’s address on the destination chain.
3. **Event Emission:** Emit an event to log the receipt and minting of tokens.

### Code Snippet

Here’s the core code snippet illustrating the `uniTransfer` and `_uniReceive` functions:

```javascript
    /* Burns user tokens on source chain and sends mint message on destination chain */
    function uniTransfer(
        uint256 _destChainSlug,
        address _destReceiver,
        uint256 _amount
    ) external payable {
        _burn(msg.sender, _amount);

        bytes memory payload = abi.encode(msg.sender, _destReceiver, _amount);

        ISocket(socket).outbound{value: msg.value}(
            _destChainSlug,
            destGasLimits[_destChainSlug],
            bytes32(0),
            bytes32(0),
            payload
        );

        emit UniTransfer(_destChainSlug, _destReceiver, _amount);
    }

    /* Decodes destination data and mints equivalent tokens burnt on source chain */
    function _uniReceive(
        uint256 siblingChainSlug_,
        bytes calldata payload_
    ) internal {
       (address _sender, address _receiver, uint256 _amount) = abi.decode(
            payload_,
            (address, address, uint256)
        );

        _mint(_receiver, _amount);

        emit UniReceive(_sender, _receiver, _amount, siblingChainSlug_);
    }
```

## Practical Use Cases

Universal ERC20 tokens are instrumental for applications that demand seamless token interoperability across multiple blockchain ecosystems. They can be employed in DeFi protocols, cross-chain decentralized exchanges, multi-chain NFT platforms, and various other blockchain applications desiring an integrated, efficient, and secure mechanism for cross-chain token transfers.
