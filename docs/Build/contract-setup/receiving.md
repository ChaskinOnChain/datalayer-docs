---
id: receiving
title: Receiving messages
sidebar_position: 3
---

Plugs on the destination network implement an `inbound()` method to process incoming messages. This method is called upon by Socket to deliver the payload.

### Implementing the `Inbound()` Method

Socket calls the `inbound` function on the destination Plug when sending the payload. This function is tasked with executing the received message from the source chain.

```javascript
interface IPlug {
    /**
     * @notice executes the message received from source chain
     * @dev this should be only executable by socket
     * @param siblingChainSlug_ chain slug of source
     * @param payload_ the data which is needed by plug at inbound call on destination
     */
    function inbound(
        uint32 siblingChainSlug_,
        bytes calldata payload_
    ) external;
}
```

:::info Security Note
Ensure that only the [`Socket`](../../dev-resources/Deployments.mdx) is authorized to call the `inbound()` method on your Plug. This restriction enhances security and integrity in message handling.
:::

### Example: Handling Incoming Messages

Below is a practical example of a Plug configured to receive and process incoming messages. In this instance, we're decoding the payload into a `uint256` data type.

```javascript
uint256 number; // A variable to store the decoded payload

function inbound(
uint32 siblingChainSlug*,
bytes calldata payload*
) external payable {
// Ensure the call is made by the Socket only
require(msg.sender == address(socket), "Caller is not the Socket");
    // Decode and process the payload
    number = abi.decode(payload_, (uint256));  // Adjust the data type as per your requirements
}
```
