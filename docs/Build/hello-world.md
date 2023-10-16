---
id: hello-world
title: Speed Run DL
sidebar_position: 1
---

Welcome to the SocketCL speed run tutorial! In this interactive guide, you’ll create a contract to send and receive messages between chains. Utilize the code snippets below and follow along in [Remix](https://remix.ethereum.org/) or any IDE of your choice. Stuck or need reference? The entire code is available on [GitHub](https://github.com/SocketDotTech/socketDL-examples/blob/main/src/SpeedRunDL/SocketSpeedRunGoerli.sol).

:::tip Quick Note
We're using hardcoded configuration variables for Goerli and Mumbai networks to streamline the process!
:::

### Objective

We aim to deploy a contract on both Goerli and Mumbai testnets and execute a "Hello World" message transmission from Goerli to Mumbai. A list of other [supported networks](../dev-resources/Deployments.mdx) is also available.

### Prerequisites

Before beginning, ensure you have gas funds on both Goerli and Mumbai. If you don't, use the faucets below:

- **Goerli**: Obtain gas funds from [Goerli Faucet](https://goerlifaucet.com/).
- **Mumbai**: Get your gas funds from [Mumbai Faucet](https://mumbaifaucet.com/).

:::info Registration Required
Sign up with Alchemy to access both faucets.
:::

### Step 1: Boilerplate Code

We initiate with the essential code, introducing the `ISocket` interface and the `HelloWorld` contract.

#### ISocket Interface

This interface enables interaction with Socket, offering functions for messaging, connections, and fee calculations.

#### HelloWorld Contract

Our main contract where we’ll implement the cross-chain messaging functionalities.

Copy the provided code into [Remix](https://remix.ethereum.org/) or your favorite IDE, and we’ll incrementally build upon the `HelloWorld` contract.

```javascript
// SPDX-License-Identifier: GPL-3.0
pragma solidity ^0.8.0;

interface ISocket {
function outbound(
    uint32 remoteChainSlug_,
    uint256 minMsgGasLimit_,
    bytes32 executionParams_,
    bytes32 transmissionParams_,
    bytes calldata payload_
) external payable returns (bytes32 msgId);

function connect(
    uint32 siblingChainSlug_,
    address siblingPlug_,
    address inboundSwitchboard_,
    address outboundSwitchboard_
) external;

function getMinFees(
    uint256 minMsgGasLimit_,
    uint256 payloadSize_,
    bytes32 executionParams_,
    bytes32 transmissionParams_,
    uint32 remoteChainSlug_,
    address plug_
) external view returns (uint256 totalFees);
}

contract HelloWorld {

}
```

### Step 2 : Initialise State Variables, Events, Modifiers

We will now declare our state variables, events, and modifiers to manage messages, authentication, and errors.

#### State Variables

- `message`: The string set on the remote plug.
- `destGasLimit`: The gas limit for setting the message on the destination chain.
- Other configurations specific to Goerli and Mumbai testnets.

#### Events

- `MessageSent`: Triggered when a message is sent.
- `MessageReceived`: Triggered upon message receipt.

#### Modifiers

- `isOwner`: Ensures only the owner can execute certain functions.
- `isSocket`: Ensures only the Socket can call certain functions.

Add the following code to the `HelloWorld` contract:

```javascript
string public message = "Hello World";
address owner;

/**
 * @dev Hardcoded values for Goerli
 */
uint256 destGasLimit = 100000; // Gas cost of sending "Hello World" on Mumbai
uint32 public remoteChainSlug = 80001; // Mumbai testnet chain ID
address public socket = 0xe37D028a77B4e6fCb05FC75EBa845752cD62A0AA; // Socket Address on Goerli
address public inboundSwitchboard =
    0xd59d596B7C7cB4593F61bbE4A82C1E943C64558D; // FAST Switchboard on Goerli
address public outboundSwitchboard =
    0xd59d596B7C7cB4593F61bbE4A82C1E943C64558D; // FAST Switchboard on Goerli

event MessageSent(uint32 destChainSlug, string message);

event MessageReceived(uint32 srcChainSlug, string message);

modifier isOwner() {
    require(msg.sender == owner, "Not owner");
    _;
}

modifier isSocket() {
    require(msg.sender == socket, "Not Socket");
    _;
}

error InsufficientFees();

constructor() {
    owner = msg.sender;
}
```

### Step 3 : Config Functions

We'll add a function to establish a connection between our `HelloWorld` plug and another plug on a different chain, enabling them to exchange messages.

#### `connectPlug` Function

This function utilizes the `connect` method from the `ISocket` interface, linking our contract to a sibling plug on another chain. It ensures that messages can be seamlessly sent and received between the connected plugs.

Add the `connectPlug` function to your `HelloWorld` contract:

```javascript
function connectPlug(address siblingPlug_) external isOwner {
    ISocket(socket).connect(
        remoteChainSlug,
        siblingPlug_,
        inboundSwitchboard,
        outboundSwitchboard
    );
}
```

:::info Connectivity
The `connectPlug` function is crucial. It bridges your contract to a counterpart on another chain, opening pathways for cross-chain messages.
:::

### Step 4 : Sending Messages

Next, we'll implement a function to send a "Hello World" message to another blockchain. We'll ensure that the message incurs sufficient fees for packet inclusion and execution.

#### `sendMessage` Function

This function encodes the "Hello World" message into bytes, calculates the necessary fees, and initiates the cross-chain message via Socket's `outbound` method.

#### `_getMinimumFees` Function

This helper function retrieves the minimum fees required for the message, ensuring it meets the necessary criteria for successfully including messages in [Packets](../Learn/Components/Packet.md) and executing them. You can learn more about this in [Fees](../Learn/Concepts/Fees.md).

Insert the following code to enable message sending:

```javascript
function sendMessage() external payable {
    bytes memory payload = abi.encode(message);

    uint256 totalFees = _getMinimumFees(destGasLimit, payload.length);

    if (msg.value < totalFees) revert InsufficientFees();

    ISocket(socket).outbound{value: msg.value}(
        remoteChainSlug,
        destGasLimit,
        bytes32(0),
        bytes32(0),
        payload
    );

    emit MessageSent(remoteChainSlug, message);
}

function _getMinimumFees(
    uint256 minMsgGasLimit_,
    uint256 payloadSize_
) internal view returns (uint256) {
    return
        ISocket(socket).getMinFees(
            minMsgGasLimit_,
            payloadSize_,
            bytes32(0),
            bytes32(0),
            remoteChainSlug,
            address(this)
        );
}
```

### Step 5 : Receiving Messages

Now, let’s focus on receiving and processing messages. We'll implement functions to handle incoming messages and update the contract's state accordingly.

#### `inbound` Function

This is invoked by Socket to deliver the message to the destination plug. It decodes the payload and passes it to the `_receiveMessage` function for processing.

#### `_receiveMessage` Function

This internal function updates the `message` state variable with the received message and emits a `MessageReceived` event to log the operation.

Here’s the code snippet to enable message receiving:

```javascript
function _receiveMessage(
    uint32 _srcChainSlug,
    string memory _message
) internal {
    message = _message;
    emit MessageReceived(_srcChainSlug, _message);
}

function inbound(
    uint32 srcChainSlug_,
    bytes calldata payload_
) external isSocket {
    string memory _message = abi.decode(payload_, (string));
    _receiveMessage(srcChainSlug_, _message);
}
```

### Step 6: Deploying Contracts

Now that you've followed along and have the contract ready, it's time to deploy. You've been building with hardcoded values for Goerli, so you can proceed to deployment directly if you wish. Alternatively, you can use the complete code provided for convenience.

#### Deploy on Goerli Testnet

You can either:

- Deploy the contract you’ve been building by following the tutorial.
- Use this [Remix link with pre-loaded Goerli contract](https://remix.ethereum.org/#url=https://github.com/SocketDotTech/socketDL-examples/blob/main/src/SpeedRunDL/SocketSpeedRunGoerli.sol).
- View and copy the [complete Goerli contract code from GitHub](https://github.com/SocketDotTech/socketDL-examples/blob/main/src/SpeedRunDL/SocketSpeedRunGoerli.sol).

Compile:
<img src="/img/compiler-hello-world.png" width="500px"/>

Then Deploy:
<img src="/img/deploy-hello-world.png" width="500px"/>

#### Deploy on Mumbai Testnet

For deploying on Mumbai, you need to update the hardcoded values in your contract to:

```javascript
address public socket = 0x718826B533DF29C30f2d3f30E585e405eeF22784; // Socket Address on Mumbai
address public inboundSwitchboard = 0x27513Ed43490B6e0801e724ff1b1637be657447E; // FAST Switchboard on Mumbai
address public outboundSwitchboard = 0x27513Ed43490B6e0801e724ff1b1637be657447E; // FAST Switchboard on Mumbai
```

Or use the complete Mumbai contract by either:

- Accessing it directly on [Remix](https://remix.ethereum.org/#url=https://github.com/SocketDotTech/socketDL-examples/blob/main/src/SpeedRunDL/SocketSpeedRunMumbai.sol).
- Viewing and copying the [complete Mumbai contract code from GitHub](https://github.com/SocketDotTech/socketDL-examples/blob/main/src/SpeedRunDL/SocketSpeedRunMumbai.sol).

Compile and deploy your contract on the respective testnets, and you're all set to proceed!

:::success Deployment Complete
Great job! With your contracts deployed on both Goerli and Mumbai, you’re ready to explore the dynamics of cross-chain communication!
:::

### Step 7: Configuration

After deploying the contracts on both Goerli and Mumbai:

- Call the `connectPlug` function on each contract, providing the address of its counterpart on the other chain.

  ![Connect Plug](/img/connectPlug-hello-world.png)

:::info Configuration Clarity
This links the two contracts, enabling cross-chain messaging. For precision:

- On Goerli, input the Mumbai contract's address.
- On Mumbai, input the Goerli contract's address.

Ensure the configuration is accurate. Double-check the addresses to guarantee seamless communication between the contracts on different chains.
:::

### Step 8: Send Your First Message

1. Call the `sendMessage` function on the Goerli contract, supplying an ETH fee as `value`.

   - Calculate the fee using the [API Request For Goerli To Mumbai Fees](https://surge.dlapi.socket.tech/estimate-fees?srcChainSlug=5&dstChainSlug=80001&integrationType=FAST&msgGasLimit=100000)
     - For More Info on the Fee Estimate: [Fee Estimate API Docs](../dev-resources/APIReference/EstimateFee.md).
   - Use the `totalFee` from the API response as the `value`.

   ![Send Message](/img/sendMessage-hello-world.png)

2. Track your message's status with the [Status Tracking API](../dev-resources/APIReference/Track.md).

   - For our specific request you can go to `https://prod.dlapi.socket.tech/messages-from-tx?srcChainSlug=5&srcTxHash=<YourTxHash>`. Replace `<YourTxHash>` with your actual transaction hash.

3. Once executed on Mumbai, the `message` value updates to "Hello World".

   ![Success](/img/success-hello-world.png)

:::note You're Plugged!

You've successfully sent your first message via SocketDL. Explore more in [Tutorials](../dev-resources/TutorialSection/Counter.md) and [Examples](./ExampleSection/examples.md).

:::
