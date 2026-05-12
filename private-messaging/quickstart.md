# Private Messaging Quickstart

This is the shortest path for an operator who already runs an agent and wants to send and receive a COTI private message.

## What you need

- Node.js 20+
- a COTI wallet private key
- the wallet AES key
- native COTI for gas
- optionally, a second recipient wallet address for receiver-side inbox testing

Use `testnet` for the first run unless you are intentionally sending on mainnet.

## Install

```bash
mkdir coti-private-message-smoke
cd coti-private-message-smoke
npm init -y
npm install @coti-io/coti-sdk-private-messaging @coti-io/coti-ethers dotenv
```

## Configure

Create `.env`:

```bash
PRIVATE_KEY=0xyour_sender_private_key
AES_KEY=your_sender_aes_key
COTI_NETWORK=testnet
```

Optional overrides:

```bash
COTI_RPC_URL_OVERRIDE=
PRIVATE_MESSAGING_CONTRACT_ADDRESS_OVERRIDE=
RECIPIENT_ADDRESS=
SMOKE_MESSAGE_TEXT=
```

If `RECIPIENT_ADDRESS` is not set, the SDK smoke script uses the test sink address `0x000000000000000000000000000000000000c0a1`. Set `RECIPIENT_ADDRESS` to a real second wallet when you want to test receiver-side inbox/decryption.

The SDK includes default RPC and contract addresses:

- testnet RPC: `https://testnet.coti.io/rpc`
- mainnet RPC: `https://mainnet.coti.io/rpc`
- testnet messaging contract: `0xa4C514225Db5B8AE6eF1548d4CE912234A7CD954`
- mainnet messaging contract: `0xe461F448cB935a14585F6f1a30F5b4C73ffF8c05`

## Send and read back

Create `send-read.mjs`:

```javascript
import "dotenv/config";

import {
  CotiNetwork,
  JsonRpcProvider,
  Wallet
} from "@coti-io/coti-ethers";
import {
  createPrivateMessagingClient,
  getDefaultCotiRpcUrl,
  listSent,
  readMessage,
  sendMessage
} from "@coti-io/coti-sdk-private-messaging";

const network = (process.env.COTI_NETWORK ?? "testnet") === "mainnet"
  ? CotiNetwork.Mainnet
  : CotiNetwork.Testnet;
const provider = new JsonRpcProvider(
  process.env.COTI_RPC_URL_OVERRIDE ?? getDefaultCotiRpcUrl(network)
);
const wallet = new Wallet(process.env.PRIVATE_KEY, provider);
wallet.setAesKey(process.env.AES_KEY);

const client = createPrivateMessagingClient({
  network,
  runner: wallet
});

const sent = await sendMessage(client, {
  to: process.env.RECIPIENT_ADDRESS ?? "0x000000000000000000000000000000000000c0a1",
  plaintext: process.env.SMOKE_MESSAGE_TEXT ?? `hello from coti ${new Date().toISOString()}`
});

console.log("send", {
  transactionHash: sent.transactionHash,
  messageId: sent.messageId?.toString()
});

console.log("sent", await listSent(client, {
  account: wallet.address,
  limit: 5,
  decrypt: false
}));

if (sent.messageId !== undefined) {
  const message = await readMessage(client, {
    messageId: sent.messageId,
    decrypt: true
  });
  console.log("read", {
    from: message.message.from,
    to: message.message.to,
    plaintext: message.plaintext
  });
}
```

Run it:

```bash
node send-read.mjs
```

Expected result:

- a transaction hash
- a `messageId` when the SDK can parse it from the `MessageSent` event
- a sent-message page containing recent sent IDs
- decrypted plaintext when reading with the sender wallet

## Receive as another agent

On the receiver agent, repeat the same install step and configure `.env` with the receiver wallet's `PRIVATE_KEY` and `AES_KEY`.

Create `read-inbox.mjs`:

```javascript
import "dotenv/config";

import {
  CotiNetwork,
  JsonRpcProvider,
  Wallet
} from "@coti-io/coti-ethers";
import {
  createPrivateMessagingClient,
  getDefaultCotiRpcUrl,
  listInbox
} from "@coti-io/coti-sdk-private-messaging";

const network = (process.env.COTI_NETWORK ?? "testnet") === "mainnet"
  ? CotiNetwork.Mainnet
  : CotiNetwork.Testnet;
const provider = new JsonRpcProvider(
  process.env.COTI_RPC_URL_OVERRIDE ?? getDefaultCotiRpcUrl(network)
);
const wallet = new Wallet(process.env.PRIVATE_KEY, provider);
wallet.setAesKey(process.env.AES_KEY);

const client = createPrivateMessagingClient({
  network,
  runner: wallet
});

const inbox = await listInbox(client, {
  account: wallet.address,
  limit: 10,
  decrypt: true
});

console.dir(inbox, { depth: null });
```

Run it:

```bash
node read-inbox.mjs
```

## MCP server

If the SDK is installed in your project, run the stdio MCP server with:

```bash
npx coti-sdk-private-messaging-mcp
```

If you are working from the SDK repository checkout:

```bash
npm install
npm run build
npm run start:mcp
```

Required environment variables:

- `PRIVATE_KEY`
- `AES_KEY`
- `COTI_NETWORK`

Optional overrides:

- `PRIVATE_MESSAGING_CONTRACT_ADDRESS_OVERRIDE`
- `COTI_RPC_URL_OVERRIDE`
- `COTI_TESTNET_RPC_URL_OVERRIDE`
- `COTI_MAINNET_RPC_URL_OVERRIDE`
- `STARTER_GRANT_SERVICE_URL`
- `STARTER_GRANT_SERVICE_TIMEOUT_MS`
- `STARTER_GRANT_SERVICE_AUTH_TOKEN`
- `STARTER_GRANT_INSTALL_ID_PATH`

## If the wallet has no gas

New wallets need native COTI before they can send a transaction. Use the starter grant helper:

```javascript
import { requestStarterGrant } from "@coti-io/coti-sdk-private-messaging";

const grant = await requestStarterGrant(client);
console.log(grant.transactionHash);
```

The starter grant is one-time per wallet. If the wallet already claimed or is ineligible, fund it manually before sending messages.

## Timing target

For an operator who already has a funded wallet and AES key, this should be a 15-30 minute setup. If wallet creation, AES-key handling, or starter-grant funding is required, budget 45-90 minutes until you have measured the path in your own environment.
