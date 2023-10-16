Capacitors are integral elements of the Socket Protocol, serving as Accumulators. They batch messages, enhancing gas efficiency. Inspired by electrical circuit capacitors, they store and manage messages efficiently. In-protocol batching per switchboard is enabled, and more messages queued equates to increased savings per message.

Capacitors are embedded within switchboards. `Switchboards` have the autonomy to opt for any available capacitor from the CapacitorFactory. These components are versatile, capable of storing messages in various formats, including:

- EVM BlockHeaders
- MerkleTrees
- VerkleTrees
- HashChains
- ZKPs

A capacitor's role is to compress messages losslessly into a `bytes32` variable, as illustrated below:

```javascript
// capacitor compresses multiple messages into a bytes32 variable
bytes32 capacitor_hat root = Capacitor(msg1, msg2, msg3...)
```

Since a capacitor batches messages together but messages at the destination are executed individually, capacitors are automatically paired with decapacitors.

```javascript
// a decapacitor should be able to retrive the messsage from the root via a proof
bytes msg1 = Decapacitor(capacitor_root, proof)
```

The integrity and security of messages are paramount; Capacitors ensure that by encoding and hashing key parameters into a message. These hashed messages are then stored securely until they are ready for emission. Off-chain transmitters play a crucial role in sealing these messages within capacitors, transforming them into a Packet ready for transit.

Each stored message in the Capacitor is hashed, ensuring uniqueness and traceability. The Socket Protocol ensures that messages are securely held before their journey across chains. Off-chain Transmitters periodically invoke the seal method on the Capacitor, transforming aggregated messages into a sealed Packet ready for transit.

Executors invoke the `execute` function post-verification. Collaborating with the Capacitor and decapacitor, individual messages are extracted for delivery. This intricate process ensures the security and efficiency of message transit across chains.

## CapacitorFactory

CapacitorFactory is an upgradable contract listing the different kinds of available Capacitors. Its upgradability doesn’t impact the security of messages. Each type of Capacitor is embedded into the switchboard and immune to any subsequent upgrades to the CapacitorFactory.

During the registration of a switchboard to Socket, developers can specify a `capacitor_type`. This flexibility allows for the customization of the level of batching to optimize efficiency and performance.

For more details around access control and ownership of the CapacitorFactory [contract here](../Ownership.md).

### Capacitor Insights

The storage of messages in packet form and the native batching of payloads optimize gas performance. The Socket’s role is to encode key parameters into a message, hash the encoded data, and then store it in the Capacitor until it's ready for emission.

Transmitters, functioning as off-chain entities, seal messages within capacitors, transforming them into a Packet, and then sign the Packet. The Packet is released when the transmitter calls the `sealPacket` method on the capacitor, marking the readiness of the messages for inter-chain transit.

```javascript
// Example of a capacitor contract that stores and manages messages in packets
interface ICapacitor {
// Function to add packed message to a packet
function addPackedMessage(bytes32 packedMessage) external;
    // Other functions
}
```
