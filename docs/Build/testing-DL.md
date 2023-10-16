---
id: testing-dl
title: Testing DL Integration
sidebar_position: 3
---

In this section, we will cover the use of MockSocket for local testing and methods to verify the connection of your Plugs.

### Utilizing MockSocket for Local Testing

MockSocket is a valuable tool designed to facilitate the local testing of your Plugs’ integration with Socket. It emulates Socket's functionality, allowing developers to test the `inbound` and `outbound` message transactions without initiating a cross-chain message.

Detailed examples of utilizing MockSocket for local testing can be found in the [SocketDL-examples repository on GitHub](https://github.com/SocketDotTech/socketDL-examples/tree/main/test).

:::note Important
Remember, MockSocket is designed for local testing and is not suitable for end-to-end testing of message delivery, as it does not account for factors like message validity and dynamic gas prices. For comprehensive testing, deploy Plugs on [supported testnets](../dev-resources/Deployments.mdx).
:::

### Verifying Plug Connection

#### Using the API

Employ the [Check Connection helper API](../dev-resources/APIReference/CheckConnection.md) to ascertain that a connection has been effectively established between two Plugs.

#### On-Chain Verification

Post-connection, validate its success by invoking the `getPlugConfig()` method on the Socket. This view function returns the configuration of the plug for a specified remote chain. A returned configuration indicates a successful connection.

```javascript
// Example script to return the Goerli Plug configuration for connection to Mumbai Testnet Chain

const { Contract, providers } = require("ethers");

// Define the ABI, RPC endpoint, and contract address
const ABI = [
  /* ABI details here */
];
const RPC = "https://rpc.ankr.com/eth_goerli";
const CONTRACT_ADDRESS = "0xA78426325b5e32Affd5f4Bc8ab6575B24DCB1762";

// Specify the Plug address and sibling chain slug
const PLUG_ADDRESS = "0x876B15bc0963C3c1AcA50Adfc0685A458449E41d";
const SIBLING_CHAIN_SLUG = 80001;

const main = async () => {
  const provider = new providers.JsonRpcProvider(RPC);
  const contract = new Contract(CONTRACT_ADDRESS, ABI, provider);
  const plugConfig = await contract.getPlugConfig(
    PLUG_ADDRESS,
    SIBLING_CHAIN_SLUG
  );
  console.log(plugConfig);
};

main();
```

:::tip
Always ensure your ABI, RPC endpoint, and other configuration details are correct and up-to-date to achieve accurate and reliable results.
:::
