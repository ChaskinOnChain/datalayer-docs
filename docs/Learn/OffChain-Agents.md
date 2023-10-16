---
id: OffChain-Agents
title: Off-Chain Agents
---

There are a couple of off-chain agents running at all times to process cross-chain messages, this section will outline some of those. The only 2 cross-chain agents that are part of Socket Protocol are the following:

### **Transmitters**

Transmitters act as the custodians of message packets from the source chain to the destination chain. They play a pivotal role in ensuring the liveliness of the Socket Protocol, as outlined below:

- **Sealing Packets:** Transmitters are tasked with sealing packets on the source chain, an essential step towards their finalization.
- **Proposing Packets:** Once sealed, Transmitters propose these packets to the destination chain, ensuring their readiness for execution.
- **Ensuring Liveliness:** While their role is fundamental, Transmitters are designed to ensure the system’s liveliness without having the authority to censor packets.
- **Sequential Sealing:** The protocol’s design necessitates sequential sealing, ensuring that no packet can be overlooked or intentionally omitted.

### **Executors**

Executors, on the other hand, are charged with the execution of messages derived from packets on the destination chain. Here’s a look at their role:

- **Executing Messages:** Executors are responsible for processing messages from the packets by invoking the inbound function on the Plugs.
- **On-Chain Actions:** Though they operate off-chain, Executors facilitate essential on-chain actions, ensuring that messages are not only received but also effectively processed.

Apart from the above there are no native off-chain agents to Socket protocol. Depending on the switchboard you leverage there may or may not be an off-chain agent involved there.
