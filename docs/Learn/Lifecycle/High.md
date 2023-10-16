---
id: High-Level
title: Learn By Example
sidebar_position: 1
---

Lifecycle of a Transaction: Sending a Message from Optimism to Polygon

### 1. **Initiating the Transaction**

Alice wants to send a message from a contract on Optimism to update a state variable on Polygon to say "Hi World". She starts by interacting with a contract on Optimism that calls a [`Plug`](./Components/Plugs) that's plugged in to a contract on Polygon and connected to the Socket contract on Optimism. She prepares her message payload and is ready to initiate the cross-chain communication.

### 2. **Estimating the Fees**

Alice ensures the transaction has adequate fees. She uses an off-chain Fee Estimation API, inputting the source Plug's address, source chain slug (Optimism), destination chain slug (Polygon), and the gas limit required to execute the payload on Polygon. The API returns a minimum fee, ensuring the successful transit of Alice's message across the chains.

### 3. **Message Storage in the Capacitor**

Alice’s message, encoded and hashed, is stored in a [`Capacitor`](./Components/Capacitor) on Optimism. The encapsulation of Alice's message in the Capacitor is timestamped and awaits the formation of a Packet.

:::info
The Capacitor acts as an interim storage for messages before they are batched into Packets. This step is crucial for optimizing gas performance and ensures that messages are securely held before they embark on their journey across the chains.
:::

### 4. **Forming the Packet**

The off-chain `Transmitters` seal the Capacitor, transforming Alice’s and other stored messages into a [`Packet`](./Components/Packet). Alice’s message is securely contained within this Packet, identifiable and retrievable.

### 5. **Packet Transfer and Proposal**

Transmitters transfer the Packet to Polygon and propose it for verification. Every detail, including Alice’s message, is intact and accounted for, with the entire Packet's integrity endorsed by the Transmitter’s signature.

:::tip
The signature is a hallmark of authenticity, ensuring that messages remain untampered from source to destination.
:::

### 6. **Verification Process**

The Fast [`Switchboard`](./Components/Switchboard), set with a 3-minute verification window, begins the authorization process. `Watchers` oversee this process, ensuring each message, including Alice's, is authenticated. The Fast Switchboard systematically confirms the validity of Alice’s message within the Packet.

:::info
The developer chose the Fast Switchboard from the options provided by Socket Labs. However, the protocol’s design allows for complete flexibility in this choice. Registering a Switchboard is entirely permissionless, enabling developers to create and use their custom Switchboards tailored to their specific needs and preferences.
:::

### 7. **Message Execution**

Post-verification, a third-party relayer or the application itself automatically triggers the execution once the Packet is verified. The `Executors` invoke the `execute` function. The Capacitor, paired with a decapacitor, extracts Alice’s "Hi World" message, along with other individual messages from the compressed batch.

### 8. **State Update on Polygon**

The destination Plug on Polygon receives Alice’s "Hi World" message and updates the state variable accordingly. The transaction is deemed successful, and Alice’s updated message of "Hi World" is permanently stored on the Polygon blockchain.

### 9. **Notification to Alice**

Alice receives an automated notification, confirming that her message traversed the chains, passed verification, and successfully updated the state on Polygon. She can trace the journey, from the initial storage in the Capacitor on Optimism to the final state change on Polygon, assured of the security and efficiency of the process.

### Conclusion

Alice’s journey underscores Socket’s intricate design, ensuring each message’s security and traceability through every phase of cross-chain communication. Alice's message, though mingled with others, remains distinct and traceable, testament to Socket's precision and reliability in managing blockchain interoperability.
