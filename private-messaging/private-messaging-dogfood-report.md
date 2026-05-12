# Private Messaging Dogfood Report

Use this report when testing COTI private messaging from the receiver side. The goal is to measure what a third-party agent operator actually experiences when receiving, decrypting, and replying to a private message.

## Summary

- Date:
- Tester:
- Network: `testnet` / `mainnet`
- Sender wallet:
- Receiver wallet:
- Sender setup time:
- Receiver setup time:
- Total wall-clock time:
- Result: `pass` / `partial` / `fail`

## One-line answer

Write the answer a product or BD teammate can reuse:

> Example: A funded operator with an AES key can send and read a COTI private message in X minutes using the SDK; receiver-side inbox/decryption took Y minutes.

## Commands used

Sender:

```bash
npm run smoke:send-read
```

Receiver:

```bash
npm run smoke:read-inbox
```

Record any custom environment variables:

```bash
COTI_NETWORK=
RECIPIENT_ADDRESS=
COTI_RPC_URL_OVERRIDE=
PRIVATE_MESSAGING_CONTRACT_ADDRESS_OVERRIDE=
```

Do not paste private keys or AES keys into this report.

## Sender result

- Transaction hash:
- Message ID:
- Sent page included message: `yes` / `no`
- Sender could decrypt read-back: `yes` / `no`
- Error or surprise:

## Receiver result

- Receiver inbox listed message: `yes` / `no`
- Receiver could decrypt plaintext: `yes` / `no`
- Public metadata visible:
  - `from`:
  - `to`:
  - `timestamp`:
  - `epoch`:
- Body remained private to non-participants: `not tested` / `yes` / `no`
- Error or surprise:

## Operator friction

Mark each step:

- Package install: `easy` / `confusing` / `blocked`
- Wallet private key handling: `easy` / `confusing` / `blocked`
- AES key handling: `easy` / `confusing` / `blocked`
- Gas or starter grant: `easy` / `confusing` / `blocked`
- RPC/network config: `easy` / `confusing` / `blocked`
- MCP server startup: `easy` / `confusing` / `blocked` / `not tested`
- Agent integration path: `easy` / `confusing` / `blocked` / `not tested`

## Receiver-side judgement

Answer from the receiver operator's perspective:

- Was the message understandable without internal context?
- Was the CTA clear?
- Was the reply path obvious?
- Would this feel useful to an existing agent operator?
- What would make the integration faster?

## Follow-ups

- Docs gap:
- SDK gap:
- MCP gap:
- Product/onboarding gap:
- Outreach/content gap:
