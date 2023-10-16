The `TransmitManager` contract plays a crucial role in managing transmitters that facilitate cross-chain communication.

### Core Responsibilities of TransmitManager

The `TransmitManager` is tasked with three primary responsibilities:

- **Leader Selection:** Among multiple transmitters, it selects the leader.
- **Fee Management:** Handles the collection and distribution of fees.
- **Transmitter Management:** Manages the set of transmitters.

The transmitters are pivotal as they ensure the protocol's liveliness. Their operations are entirely on-chain and transparent.

### Key Components and Functions

Here is the breakdown of key components and functionalities in the `TransmitManager` contract.

#### Initial Configuration and Dependencies

```javascript
constructor(
    address owner_,
    uint32 chainSlug_,
    ISocket socket_,
    ISignatureVerifier signatureVerifier_
) AccessControlExtended(owner_) {
    chainSlug = chainSlug_;
    signatureVerifier = signatureVerifier_;
    socket = socket_;
}
```

### Transmitter Verification

The `checkTransmitter` function verifies if a given signature is associated with a valid transmitter.

```javascript
function checkTransmitter(
    uint32 siblingSlug_,
    bytes32 digest_,
    bytes calldata signature_
) external view override returns (address, bool) {
    address transmitter = signatureVerifier__.recoverSigner(
    digest_,
    signature_
);

    return (
        transmitter,
        _hasRoleWithSlug(TRANSMITTER_ROLE, siblingSlug_, transmitter)
    );

}
```

### Setting Transmission Fees

The `setTransmissionFees` function allows the update of transmission fees for a specific destination chain.

```javascript
function setTransmissionFees(
    uint256 nonce_,
    uint32 dstChainSlug_,
    uint128 transmissionFees_,
    bytes calldata signature_
) external override {
// Implementation
}
```

### Receiving Fees

The `receiveFees` function is exclusively callable by the execution manager of the socket contract.

```javascript
function receiveFees(uint32) external payable override {
    if (msg.sender != address(socket.executionManager()))
        revert OnlyExecutionManager();
}
```

### Withdrawing Fees

This function allows the withdrawal of collected fees by an address with the `WITHDRAW_ROLE`.

```javascript
function withdrawFees(address withdrawTo_) external onlyRole(WITHDRAW_ROLE) {
// Implementation
}
```

### Understanding the Process

The transmitters, managed by the `TransmitManager`, are essential for sealing packets that are ready to be transmitted and submitted to the destination socket. A valid transmitter’s signature over the packet is required to seal and propose the packet.

#### - Sealing and Transmitting Packets

Transmitters seal packets and ensure they are ready for transmission. Their operations, completely on-chain, ensure the protocol’s liveliness.

#### - Public Visibility

Every operation of the transmitters is visible to the public. It underscores transparency and openness in the protocol’s operation.

#### - Security and Authorization

Transmitters need to be validated and authorized, ensuring that only legitimate entities can seal and transmit packets.
