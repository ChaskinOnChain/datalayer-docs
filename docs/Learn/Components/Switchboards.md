Switchboards facilitate the connection of Plugs to the Socket through open-source, permissionless configuration bundles.

### Understanding Switchboards

Switchboards serve as intermediaries, authenticating and verifying packets before they are executed on the Plugs. They consist of elements like packet authentication, capacitors and decapacitors for message handling, and minimum fees for packet validation.

Switchboards Contain the Following:

- Packet authenticaion via `allowPacket` method
- Capacitor to be used for that switchboard
- Decapacitor to be used for verifying message inclusion in Packet
- minFees for packet validation

#### Example of Switchboard Interface

```javascript
interface ISwitchboard {
function registerSiblingSlug(
    uint32 siblingChainSlug_,
    uint256 maxPacketLength_,
    uint256 capacitorType_,
    uint256 initialPacketCount_,
    address siblingSwitchboard_
) external;

function allowPacket(
    bytes32 root,
    bytes32 packetId,
    uint256 proposalCount,
    uint32 srcChainSlug,
    uint256 proposeTime
) external view returns (bool);

function getMinFees(
    uint32 dstChainSlug
) external view returns (uint128 switchboardFee, uint128 verificationOverheadFees);

function receiveFees(uint32 siblingChainSlug_) external payable;
}
```

### Registering Switchboards

Switchboards need to be registered to the Socket protocol to be operational. This process is permissionless, allowing anyone to create and register a Switchboard.

#### Example of Registering a Switchboard

```javascript
// In a Switchboard contract
function registerSiblingSlug(
    uint32 siblingChainSlug_,
    uint256 maxPacketLength_,
    uint256 capacitorType_,
    uint256 initialPacketCount_,
    address siblingSwitchboard_
) external {
// Implementation to register the switchboard
}
```

### Packet Authentication

Packets need to be authenticated before being processed. The allowPacket function is integral for this purpose, ensuring packets meet set criteria for processing.

#### Example of Packet Authentication

```javascript
// In a Switchboard contract
function allowPacket(
    bytes32 root,
    bytes32 packetId,
    uint256 proposalCount,
    uint32 srcChainSlug,
    uint256 proposeTime
) external view returns (bool) {
// Implementation to check if the packet is allowed
}
```

### Fee Handling

Switchboards also manage fees for packet processing. They receive and set minimum required fees, balancing security and speed as per transaction requirements.

#### Example of Receiving Fees

```javascript
// In a Switchboard contract
function receiveFees(uint32 siblingChainSlug_) external payable {
// Implementation for receiving fees
}
```

### Types of Switchboards

Socket Labs has bootstrapped some switchboards with different tradeoffs for end developers, we encourage developers to feel free to leverage these switchboards if they are suitable for their usecase.

- Optimistic Switchboard: Allow 1/N trust-minimised and cheap validation by introducing a challenge mechanism pre-execution where N parties can stop the packet from getting executed. This Switchboard will be extremely cheap to leverage but will have a latency of the challenge period.
- Fast Switchboard: Allow 1/N trust minimised validation by having all N parties attest on-chain, this is still as secure as the OptimisticSwitchboard but might cost more on-chain depending on the destination chain.
- Native Switchboard: Allows developers to leverage native verification for eg: rollup-validation whereever it's available, we have built these connectors for Polygon, Arbitrum and Optimism right now. Developers who want to leverage security of the native-security should leverage these.

#### Example of Fast Switchboard Operation

```javascript
// Pseudocode demonstrating Fast Switchboard operation
if (allWatchersAttestOnChain()) {
    validatePacket();
} else if (timeElapsed > 3 minutes) {
    validatePacket();
}
```

### Security Measures

Switchboards incorporate security protocols to avert potential threats. Tripping mechanisms pause the switchboard or paths temporarily during anomalies, ensuring system integrity.

#### Example of Tripping a Switchboard

```javascript
// In a Switchboard contract
function tripGlobal(uint256 nonce_, bytes memory signature_) external {
// Implementation to trip the switchboard globally
}
```
