---
id: glossary
title: Glossary
sidebar_position: 1
---

Understanding the terminology is crucial for grasping the workings of the protocol.

#### Core Components

- **Socket**: The central contract deployed across all supported networks, housing all core modules and functions. [More about Socket](https://github.com/SocketDotTech/socket-DL/blob/master/contracts/socket/Socket.sol).
- **Plug/Plugs**: Adapter contracts enabling smart contract applications to connect with Socket for cross-chain messaging via the [IPlug interface](../dev-resources/Interfaces/IPlug.md).
- **ChainSlug**: A unique identifier distinguishing specific network or Socket deployments, it's different from from Chain ID or Network ID on EVM networks. [Specific Chain Slugs](/dev-resources/deployment-addresses).

#### Messaging

- **Message**: The payload intended for cross-chain transmission, accompanied by relevant metadata such as the destination ChainSlug.
- **Packet**: A collection of messages transmitted between chains, validated at the destination chain via the logic in the assigned switchboard. [Explore Packets](./Components/Packet.md).
- **Sealed Packet**: A packet signed and sealed by the transmitter on the source chain, marking it ready for submission on the destination chain. [Transmitter Details](./lifecycle.md#sending-a-message).

#### Storage and Verification

- **Capacitor**: A component tasked with storing messages in packet form, optimizing gas performance through native batching of payloads. The packet is released when the transmitter calls the `sealPacket` method on the capacitor. [Capacitor Insights](./Components/Capacitors.md).
- **CapacitorFactory**: An upgradable contract listing available Capacitors, integral for message security and protocol flexibility.
- **Switchboard**: Authentication and verification modules that support custom payload verification protocols and can be permissionlessly created and deployed. [Switchboard Overview](./Components/Switchboards.md).

#### Transmission Process

- **Transmitter**: Entities mandated with the on-chain activity of transmitting packets across layers, governed by the TransmitManager.
- **TransmitManager**: Manages transmitter operations, including leader selection, fee collection, and transmitter set management. [For More](./Components/TransmitManager.md).

#### Fee Structure [(More On Fees)](http://localhost:3000/Learn/Concepts/fees)

- **Transmission Fees**: Fees attributed to transmitters for sealing and proposing packets across chains.
- **Switchboard Fees**: Compensation for watchers verifying packet validity on the destination chain.
- **Execution Fee**: Fees directed to executors for processing the message payload on the destination chain.

#### Off-Chain Agents

- **Executors**: Workers that execute messages from the Packet on the destination chain by calling the inbound function on the Plugs.
- **Watchers**: Entities that monitor and verify the validity of packets on the destination chain, their role varies depending on the switchboard used.
