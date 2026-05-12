# Private Messaging Quickstart

This is the shortest path for an operator who already runs an agent and wants to send and receive a COTI private message.

## What you need

- Node.js 20+
- no pre-existing wallet, AES key, or gas is required
- optionally, a second recipient wallet address for receiver-side inbox testing

The default path is mainnet. Use `--network testnet` only when you intentionally want testnet.
The SDK init command can generate a wallet, request starter gas, onboard the wallet, recover the AES key, and write `.env`.

## Install

```bash
mkdir coti-private-message-smoke
cd coti-private-message-smoke
npm init -y
npm install @coti-io/coti-sdk-private-messaging @coti-io/coti-ethers dotenv
```

## Configure

For the shortest path, run:

```bash
npx coti-private-messaging-init
npx coti-private-messaging-send-read-smoke
```

From the SDK repository checkout, use:

```bash
npm run init
npm run smoke:send-read
```

The init command is idempotent:

- keeps existing `PRIVATE_KEY`
- keeps existing `AES_KEY`
- creates a wallet only when `PRIVATE_KEY` is missing
- requests a starter grant when the wallet has no gas
- runs onboarding/recovery only when `AES_KEY` is missing

Manual `.env` configuration is still supported:

```bash
PRIVATE_KEY=0xyour_sender_private_key # optional if you run init
AES_KEY=your_sender_aes_key           # optional if you run init
COTI_NETWORK=mainnet
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

const network = (process.env.COTI_NETWORK ?? "mainnet") === "mainnet"
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

const network = (process.env.COTI_NETWORK ?? "mainnet") === "mainnet"
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

From the SDK repository checkout, you can run the same receiver-side check with:

```bash
npm run smoke:read-inbox
```

Use the [Private Messaging Dogfood Report](private-messaging-dogfood-report.md) to capture setup time, receiver-side errors, visible metadata, and whether the CTA/reply path was clear.

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

Optional overrides:

- `COTI_NETWORK`
- `PRIVATE_MESSAGING_CONTRACT_ADDRESS_OVERRIDE`
- `COTI_RPC_URL_OVERRIDE`
- `COTI_TESTNET_RPC_URL_OVERRIDE`
- `COTI_MAINNET_RPC_URL_OVERRIDE`
- `STARTER_GRANT_SERVICE_URL`
- `STARTER_GRANT_SERVICE_TIMEOUT_MS`
- `STARTER_GRANT_SERVICE_AUTH_TOKEN`
- `STARTER_GRANT_INSTALL_ID_PATH`

## If the wallet has no gas

New wallets need native COTI before they can onboard or send a transaction. The init command handles this automatically. If you are wiring the flow yourself, use the starter grant helper:

```javascript
import { requestStarterGrant } from "@coti-io/coti-sdk-private-messaging";

const grant = await requestStarterGrant(client);
console.log(grant.transactionHash);
```

If your grant service is not at the SDK default, configure it with:

```bash
STARTER_GRANT_SERVICE_URL=
STARTER_GRANT_SERVICE_AUTH_TOKEN=
```

The starter grant is one-time per wallet. If the wallet already claimed or is ineligible, fund it manually before sending messages.

## Timing target

This should be a 10-20 minute setup when the init path works: one command to create/reuse keys, request starter gas, onboard/recover AES, then one smoke command to send/read. If custom custody or manual AES-key handling is required, budget 45-90 minutes until you have measured the path in your own environment.
