---
id: configuring-plugs
title: Configuring Plugs
sidebar_position: 1
---

# How to Configure Your Plugs for Cross-Chain Interactions

To initiate cross-chain communication, you need to create and connect Plugs on the chains involved. This process involves linking the Plugs to the Socket and ensuring each Plug references its counterpart on the connected chain. This section provides a step-by-step guide to achieve this effortlessly.

:::info Key Points

- Only Plugs can update the configuration/connection.
- Permission at the Plug level is mandatory, achievable via `owner keys` or protocol governance.
- Connections are pairwise, meaning each Plug on a chain connects to another on the desired chain.

:::

Learn more about [Plugs here](../../Learn/Components/Plugs.md). Once connected, the `Outbound` and `Inbound` methods facilitate message transmission.

## Step 1: Deploy Your Plugs

Before connecting, ensure the Plugs are deployed on the respective chains. Each Plug then connects to its sibling on the other chain to establish a communication link.

## Step 2: Gather Connection Parameters

To connect the Plugs, you need the following parameters:

| Parameter             | Description                                                                                                                            |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `siblingChainSlug`    | The ChainSlug of the network where the sibling plug resides. Check the [full list of chainSlugs](../../dev-resources/Deployments.mdx). |
| `siblingPlug`         | The address of the plug on the sibling chain.                                                                                          |
| `inboundSwitchboard`  | The switchboard address for receiving messages.                                                                                        |
| `outboundSwitchboard` | The switchboard address for sending messages.                                                                                          |

:::tip Quick Overview
Switchboards are configurable, permissionless bundles essential in the authentication and execution of packets for cross-chain data transfer. They handle packet authentication, manage storage and verification with Capacitors and Decapacitors, and set minimum fees for packet validation. Developers can use existing Switchboards or create and register their own to meet specific application needs.
:::
You can find verified Switchboards and Socket contract addresses in the [Deployments section](../../dev-resources/Deployments.mdx).

## Step 3: Execute the Connection

Call the `connect` method on the Socket to configure the Plugs on both chains. Use the parameters gathered in Step 2. Ensure you have the required permissions to execute this action.

Here is an example demonstrating the connection process:

```javascript
    ISocket socket = ISocket(socket_address);

    function connectToSocket(
        uint32 siblingChainSlug,
        address siblingPlug,
        address inboundSwitchboard,
        address outboundSwitchboard
    ) external {
        // ensure its permissioned
        require(msg.sender == AUTHORISED_CONNECTOR, "Caller is not authorised to make make connections");

        // finally just call the socket to connect
        socket.connect(
            siblingChainSlug,
            siblingPlug,
            inboundSwitchboard,
            outboundSwitchboard
        );
  }
```

## Step 4: Verify the Connection

A successful connection triggers the emission of a PlugConnected log. Verify this occurrence and confirm the connection's success.

Here's an example of a transaction emitting [PlugConnected](https://goerli.etherscan.io/tx/0x37040ec23ae744c8e4ea2961ceae8a4f32abaf04cc112c017089e95ceefb1653#eventlog).

Additionally, validate the connection by:

- Calling `getPlugConfig` on the Socket contract to fetch your Plug’s configuration for the specified ChainSlug.
- Utilizing the [Check Connection API](../../dev-resources/APIReference/CheckConnection.md) to read the configurations, ensuring the connection is accurate.

With these steps, your Plugs are now connected, and you’re set to initiate cross-chain communication seamlessly.

<!-- // TODO: ADD GIF; ITs time to send it -->
