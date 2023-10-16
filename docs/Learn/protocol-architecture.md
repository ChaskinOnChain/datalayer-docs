---
id: protocol-design
title: Protocol Design
sidebar_position: 2
---

## Introduction

Socket is a protocol designed for the secure and efficient transfer of messages between multiple blockchains. The process begins with a `Plug`, a contract implemented with the IPlug interface, which dispatches a payload to the local Socket contract. The Socket encodes key parameters into a message, hashes the encoded data, and stores it in the `Capacitor`, where it remains until ready for emission.

`Transmitters`, functioning as off-chain entities, perform multiple roles. Initially, they seal messages within capacitors, transforming them into a `Packet` and sign the Packet. Separate Transmitters then transport these Packets from the source to the destination blockchain. The Packet, endorsed with the Transmitter’s signature, is accessible on the Socket contracts on both involved chains.

At the destination chain, specialized contracts known as `Switchboards` determine the criteria for processing payloads. These criteria can be time-bound or contingent on confirmations from a native bridge, offering adaptability and robust security for handling a variety of transaction types.

:::warning
Choice of the Switchboard is critical as it defines the criteria for payload processing, balancing security and speed according to the specific transaction requirements.
:::

Capacitors, integrated within Switchboards, facilitate message batching to optimize gas efficiency. `Watchers`, depending on the specific type of Switchboard, perform varying roles. For example, in Optimistic Switchboards, Watchers are empowered to identify and flag specific paths or packets if inconsistencies or fraudulent activities are detected, fortifying the security layer of the protocol.

During the processing phase, `Executors` invoke the `execute` function, ensuring that the payload's criteria are satisfied. Subsequently, it calls the Capacitor (paired with a decapacitor) to extract individual messages from the compressed batch, and then initiates a call to the destination plug with the payload.

Developers can customize this process, tailoring the protocol's features to balance the security and speed required for their specific applications.

<img src="/img/BroadArchitecture.png" width="700px"/>

## Architecture

Together, Plugs, Socket contracts, Transmitters, Switchboards, Executors, Capacitors, and Watchers create a comprehensive ecosystem for secure and efficient cross-chain communication.

:::info
The modular nature of Socket ensures that developers have the flexibility to create a tailored communication ecosystem, meeting the unique needs and requirements of their applications.
:::

The protocol consists of four key layers:

1. **Application Layer**: Where `Plugs`, the smart contract applications designed to seamlessly interact with Socket, adhering to a predefined API, reside. Every interaction facilitated through the Plugs is routed via the Socket core contract, establishing a channel for payloads between any two supported chains. As applications dispatch payloads, Socket proficiently compresses them into `Packets` - cryptographic constructs that encapsulate a multitude of payloads spanning various applications.
2. **Delivery Layer**: Responsible for message batching and the inter-chain delivery of packets. `Capacitor` contracts store packets until they're ready to be sealed for transit. `Transmitters`, off-chain participants, deliver packets between chains, signing them on both the source and destination chains to ensure integrity and transparency. The verification layer can use this to build trustless verification schemes. Permissionless and transparent message delivery allows for an efficient marketplace for message delivery.
3. **Verification Layer**: `Switchboards` operate in this layer to verify packets and messages. These modules are customizable, allowing developers to set specific conditions for packet acceptance or rejection. Payloads on the destination chain are processed based on the conditions set by the app-selected Switchboard. Socket processes the payload only if it meets the conditions specified by the app-selected Switchboard, such as accepting payloads after a 2-hour timeout or confirming payloads through a native rollup bridge. Permissionlessness and modularity at the verification layer is a core advantage and design principle for Socket. The validity of the packets is verified by `Watchers`.
4. **Execution Layer**: Messages are executed on the destination chain in this layer after passing verification. Execution can be handled by third-party relayers like Gelato or by the applications and users themselves, with various retry mechanisms in place to ensure reliability.

<img src="/img/dl-layers.png"/>

:::tip
Every layer of the Socket protocol is meticulously designed to ensure the smooth, secure, and efficient transfer of messages across blockchains, guaranteeing both the integrity and confidentiality of data in transit.
:::

Learn more about individual components in [Components](./Components/Capacitors.md) and [Offchain Agents](./OffChain-Agents.md).
