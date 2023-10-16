---
id: example-uniERC721
title: Universal ERC721
sidebar_position: 2
---

Universal ERC721 extends the popular ERC721 token standard, integrating cross-chain transfer capabilities courtesy of SocketDL. This enhancement facilitates the seamless and secure transfer of unique tokens, like NFTs, across different blockchain networks, enhancing their accessibility and market reach.

### Key Features

- **Cross-Chain Transfers:** Enables the seamless transfer of ERC721 tokens, like NFTs, between different blockchain ecosystems.
- **Burn & Mint Mechanism:** Tokens are burnt on the source chain and minted on the destination chain, ensuring the uniqueness and scarcity of each token is preserved.
- **SocketDL Integration:** Leverages SocketDL for secure and efficient cross-chain interactions.

Discover the implementation details of Universal ERC721 on [GitHub](https://github.com/SocketDotTech/socketDL-examples/blob/main/src/universalTokens/uniERC721/uniERC721.sol).

## Understanding the Code

### `uniTransfer` Function

The `uniTransfer` function is designed to facilitate the cross-chain transfer of ERC721 tokens. It ensures the token is burnt on the source chain and initiates an outbound message to the destination chain, where the token will be minted anew.

Here are the key steps:

1. **Token Burning:** The `_burn` function is invoked to burn the specified ERC721 token.
2. **Message Encoding:** The sender's address, recipient's address, and the token ID are encoded into a payload.
3. **Outbound Message:** The encoded payload is sent to the destination chain via SocketDL’s outbound function.

### `_uniReceive` Function

Upon receipt of the inbound message on the destination chain, the `_uniReceive` function decodes the payload and mints the ERC721 token that was burnt on the source chain.

Key steps include:

1. **Payload Decoding:** Extract the sender’s address, recipient’s address, and token ID from the payload.
2. **Token Minting:** Mint the ERC721 token to the recipient’s address on the destination chain.
3. **Event Emission:** Emit an event to log the successful minting of the token.

### Code Snippet

Below is the integral code snippet depicting the `uniTransfer` and `_uniReceive` functions:

```javascript

    /* Transfers tokens between chains */
    function uniTransfer(
        uint256 _destChainSlug,
        address _destReceiver,
        uint256 tokenId
    ) external payable {
        _burn(tokenId);

        bytes memory payload = abi.encode(msg.sender, _destReceiver, tokenId);

        ISocket(socket).outbound{value: msg.value}(
            _destChainSlug,
            destGasLimits[_destChainSlug],
            bytes32(0),
            bytes32(0),
            payload
        );

        emit UniTransfer(_destChainSlug, _destReceiver, tokenId);
    }

    function _uniReceive(
        uint256 _siblingChainSlug,
        address _sender,
        address _receiver,
        uint256 _tokenId
    ) internal {
        _safeMint(_receiver, _tokenId);

        emit UniReceive(_sender, _receiver, _tokenId, _siblingChainSlug);
    }

```

## Practical Applications

Universal ERC721 tokens are particularly beneficial for NFT platforms and collectible marketplaces seeking to enhance the visibility, accessibility, and liquidity of unique digital assets. By enabling cross-chain transfers, NFT creators and collectors can reach broader audiences, participate in diverse ecosystems, and maximize the value and utility of their NFTs.
