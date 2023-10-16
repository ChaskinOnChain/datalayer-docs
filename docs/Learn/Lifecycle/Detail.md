---
id: Detailed
title: Detailed
sidebar_position: 2
---

In this section, we dive deeper into the main interactions between Plugs and Socket

- [Connecting to Socket](#connecting-to-socket)
- [Sending a Message](#sending-a-message)
- [Receiving a Message](#receiving-a-message)

### Connecting to Socket

<img src="/img/ConnectToDL.png" />

[`Plugs`](./Components/Plugs) serve as an integral component in the Socket ecosystem, initiating the bridge for cross-chain communication. To ensure the seamless interaction of messaging across diverse blockchains, a Plug must first be "plugged" into Socket. This connection entails the sharing of configuration details essential for establishing a reliable and secure communication channel.

#### Connection Process

1. **Configuration Details Submission:**
   The originating Plug sends configuration details to establish a connection. These details encompass the sibling chain ID, sibling Plug address, and specific Switchboard configuration. These configurations set the foundation for tailored communication, ensuring each message adheres to the set parameters, enhancing both security and efficiency.

2. **Unique Identifier Generation:**
   Upon receiving the configuration details, Socket proceeds to generate a unique identifier. This identifier is crucial for distinguishing each configuration, ensuring that every connection is treated individually, allowing for customized communication paths based on the Plug and destination chain involved.

3. **Event Emission:**
   The connection's completion is marked by Socket emitting a `PlugConnected` event. This event carries the config data, serving as a public acknowledgment of the successful connection. It ensures transparency and allows for real-time tracking of connections as they are established.
   - [View an example transaction](https://polygonscan.com/tx/0x58231336368ff437883ada95d30897679f64257c981ee19dab8147c0b3828d0a#eventlog).

#### Specific Considerations

- **Switchboard Selection:**
  Plugs are equipped with the flexibility to employ different [`Switchboards`](./Components/Switchboards) based on their unique use cases and security prerequisites. Registering a Switchboard is completely permissionless. Each Switchboard comes with its distinct set of criteria for message verification and processing, aligning with the varied requirements of diverse applications.

- **Connection Limitation:**
  Each Plug is restricted to connect to a single Plug on each sibling chain. This design ensures a streamlined and focused communication channel. If a new connection is established with a different sibling Plug on the same chain, it overrides the previous configuration, ensuring that each Plug maintains a singular, focused connection for enhanced security and efficiency.

- **Security Precautions:**
  It's recommended to permission this action as malicious configurations during the connection to Socket can cause unintended actions. If a connection between two plugs on a chain exists, re-connecting overrides the previous setup, a behavior that can be blocked by the Plug for enhanced security.

### Sending a Message

Connected Plugs are ready to initiate state changes across chains. The sending Plug crafts a message payload containing the encoded state changes for the receiving Plug to decode on a different chain.

 <img src="/img/SendMessageOutbound.png" />

1. **Calling the Outbound Method:**

   - The sending Plug calls the `outbound` method on Socket, injecting it with parameters including `fees`, `siblingChainSlug`, `msgGasLimit`, `payload`, `transmitterParams`, and `executorParams`.
   - Socket audits the stored configuration of the Plug, ensuring a prior established and verified connection with the destination sibling chain.

2. **Fee Deduction and Payment:**

   - Socket deducts the corresponding fees from the sending Plug. These fees are allocated to various off-chain actors integral in the message transit process.
   - Dive into the detailed fee structure and allocation [here](./Concepts/Fees.md) to understand how it aligns with the secure and efficient cross-chain message transmission.

3. **Message Encoding and Storage:**

   - Socket encodes and hashes key parameters into a message. Each hash, a unique identifier, ensures message integrity and traceability.
   - The hashed message is stored in a [`Capacitor`](./Components/Capacitor), a secure vault that aggregates messages destined for specific chains, optimizing gas efficiency through batching.

4. **Message Sealing:**
   - Off-chain transmitters periodically invoke the `seal` method on the Capacitor. This method transforms the aggregated messages into a `Packet`, sealed and ready for transit.
   - The sealing process emits a `Sealed` event, a public declaration marking the Packet's readiness for transit to the destination chain.

#### Message Security and Verification

Each message is engrained with layers of security protocols. From the moment the Plug invokes the `outbound` method to the sealing of the Packet, every step is fortified with security checks and verification protocols.

1. **Parameter Verification:**

   - Socket ensures that each parameter, from the `fees` to `executorParams`, aligns with the predefined criteria. This meticulous verification ensures that every message is tailored to the specific communication channel, enhancing both security and efficiency.

2. **Configurations Checks:**

   - The Socket verifies the stored configurations of the Plug. This step ensures that the message adheres to the established communication protocols between the sending and receiving chains, guaranteeing both the integrity and appropriateness of every message.

3. **Capacitor’s Role:**

   - Capacitors are not mere storage vaults but are integral components ensuring the integrity of messages. Each message is hashed and stored, guaranteeing its uniqueness and traceability. Capacitors, being chain-specific, ensure that messages are aggregated according to their destination, enhancing the efficiency of the transit process.

4. **Transmitter’s Responsibility:**
   - The transmitters, beyond sealing the messages into Packets, play a pivotal role in ensuring that each Packet is ready for transit. The `Sealed` event is a testament to the Packet’s integrity and readiness for the journey ahead.

In essence, the sending of a message is a ballet of intricate steps, each choreographed to ensure the message not only reaches its destination but does so with utmost integrity, security, and efficiency. Each message, though mingled with others in the Packet, retains its distinct identity, a testament to Socket’s precision and reliability in managing blockchain interoperability.

### Receiving a Message

- **Transmitter Proposal:** A transmitter proposes the sealed packet, consisting of `packetId`, `root`, `switchboard`, and `signature`, to Socket on the destination chain.
- **Validation:** If sent by an authenticated transmitter, the packet proposal is validated, and a `PacketProposed` event is emitted to signify the packet's arrival.
- **Storage:** The `packetIdRoots` mapping holds the packet, readying it for subsequent verification and execution phases.

<img src="/img/propose-packet.png" width="600px"/>

#### Verification by Watchers

- **Watchers’ Role:** Depending on the app-selected Switchboard, Watchers scrutinize packets for inconsistencies or fraudulent activities.
- **Switchboard Flexibility:** The protocol accommodates customizable and permissionless Switchboards, setting specific packet acceptance conditions and enhancing security.
- **Verification:** Watchers confirm packets’ compliance with Switchboard conditions, facilitating the transition to the execution phase.

#### Execution of the Message

<img src="/img/ExecuteMessage.png" width="600px"/>

- **Executors’ Role:** Post-verification, Executors invoke the `execute` function, collaborating with the Capacitor and decapacitor to extract individual messages for delivery.
- **Message Delivery:** The destination plug receives the payload after a meticulous verification and extraction process, ensuring precise cross-chain communication.
- **Retry Mechanisms:** n case the message cannot be executed due to insufficient gas limit or other errors, Socket will re-try execution periodically. More on [Execution Failure](../dev-resources/TutorialSection/RetryMessageExecution.md).

#### Enhanced Security and Flexibility

- **Security Protocols:** Transmitters and Watchers collaborate to assure message integrity and security. Transmitters maintain system liveliness while Watchers ensure authenticity.
- **Developer Flexibility:** The protocol's design empowers developers with the flexibility to select or create Switchboards that align with specific needs and applications.
- **Execution Triggers:** Either third-party relayers or applications and users themselves can trigger execution, ensuring accurate and reliable message delivery and execution.
