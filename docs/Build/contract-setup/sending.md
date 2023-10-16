---
id: sending
title: Sending messages
sidebar_position: 2
---

The `Outbound` method, integrated into the Socket contract deployed on all networks, facilitates sending messages across chains from one Plug to another. Before diving into the implementation, it's essential to ensure your Plugs on both chains are interconnected.

## Parameters

| Parameters      | Description                                                      |
| --------------- | ---------------------------------------------------------------- |
| remoteChainSlug | ChainSlug of the network where you want to send your messsage to |
| payload         | The message you want to send to the plug on remoteChainSlug      |
| msgGasLimit     | gasLimit required to execute the `payload` on remoteChainSlug    |

Messages incur cross-chain execution fees, payable via `msg.value` when calling the `Outbound` function. Estimate the minimum required fees using the [provided methods](/dev-resources/APIReference/estimate-fees).

### Example: Sending a "Hello, World!" Message

Here’s a step-by-step example of sending a “Hello, World!” message across chains:

1. **Ensure Plugs Are Connected**:
   Make sure your Plugs on the sender and receiver chains are interconnected.

2. **Prepare the Payload**:
   Encode the message into the payload.

3. **Call the `Outbound` Method**:
   Invoke the `Outbound` method on the Socket, including the necessary parameters and fees.

```javascript
ISocket socket = ISocket(_address); // Retrieve the Socket address for your network
uint32 remoteChainSlug = 1333; // Replace with the actual ChainSlug of the target network
uint32 defaultGasLimit = 1000000; // Set an appropriate gas limit for the transaction

function sendHelloWorld() external payable {
   bytes memory payload = abi.encode("Hello World");  // Encode the message into a payload
   require(msg.value >= minFees, "Insufficient fees");  // Ensure adequate fees are provided

   socket.outbound{value: msg.value}(remoteChainSlug, defaultGasLimit, bytes32(0), bytes32(0), payload);  // Send the message
}
```

### Message Transmission Confirmation

Once the transaction is finalized:

- A `MessageOutbound` event is emitted containing all relevant details.
- The message is allocated a unique ID (`msgId`), which can be found in the `MessageOutbound` log.

### Tracking Message Delivery

Utilize the transaction hash to track the message's delivery and execution on the destination chain via the [Tracking API](../../dev-resources/APIReference/Track.md). This enables real-time monitoring of the message status until it reaches the intended recipient.

For a detailed walkthrough of the message sending lifecycle, refer to the [comprehensive guide here](../../Learn/lifecycle.md#sending-a-message).
