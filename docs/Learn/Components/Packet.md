Packet is a cryptographic representation of PackedMessages. Capacitor produces Packets as soon as PackedMessages are added to it.

### Structuring a Packet

PackedMessages created whenever a new message is sent via the Outbound method and are compressed representation of the following:

- chainSlug: ChainSlug of the local network
- msg.sender: Src Plug for the chainSlug
- SiblingChainSlug: ChainSlug for the sibling network
- SiblingPlug: Address for the sibling Plug on RemoteChainSlug
- MessageDetails: Details about a message such as `msgId`, `minMsgGasLimit`, `executionParams`, `payload` and `executionFee`
- msgId : Unique identifier for a message
- minMsgGasLimit: Minimum gasLimit provided by the Plug for message execution on destination
- ExecutionFee: Execution fee provided by the Plug
- Payload: Message provided by the Plug to be relayed to destination
- executionParams: Additional execution details

```javascript
        bytes32 packedMessage = hasher__.packMessage(
            chainSlug,
            msg.sender,
            siblingChainSlug_,
            plugConfig.siblingPlug,
            messageDetails
        );
```

### Sealing the Packet

Transmitters, off-chain entities, seal messages within Packets. The sealing initiates via the `seal` method on the Capacitor, signifying the Packet’s transit readiness.

```javascript
function seal(
uint256 batchSize_,
address capacitorAddress_,
bytes calldata signature_
) external payable;
```

### Packet Transfer and Proposal

The secure transit of the Packet to the destination chain is overseen by Transmitters. The `proposeForSwitchboard` method outlines this transfer.

```javascript
function proposeForSwitchboard(
bytes32 packetId_,
bytes32 root_,
address switchboard_,
bytes calldata signature_
) external payable;
```

### Receiving and Verifying a Message

Packets arriving at the destination chain undergo rigorous verifications. The `packetIdRoots` function aids in retrieving roots for specified packet IDs.

```javascript
function packetIdRoots(
bytes32 packetId_,
uint256 proposalCount_,
address switchboard
) external view returns (bytes32);
```

### Execution of the Packet

The culmination of the Packet’s journey is the execution of encapsulated messages. The `execute` method in the ISocket interface governs this phase.

```javascript
function execute(
ISocket.ExecutionDetails calldata executionDetails_,
ISocket.MessageDetails calldata messageDetails_
) external payable;
```
