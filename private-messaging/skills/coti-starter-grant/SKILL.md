---
name: coti-starter-grant
description: >-
  Guides one-time COTI gas funding for new wallets via MCP request_starter_grant,
  get_starter_grant_status, get_starter_grant_challenge, and claim_starter_grant.
  Use when onboarding a wallet with no gas, first-time funding, or when the user mentions
  starter grant, challenge flow, or empty COTI balance before messaging.
disable-model-invocation: true
---

# COTI Starter Grant

Handles one-time gas funding for a newly created wallet.

## Overview

New wallets cannot interact with COTI until they have some native COTI for gas. This workflow wraps the challenge-response flow used to fund an eligible wallet for the first time.

## Prerequisites

- the private messaging MCP server must be connected
- a wallet must already be configured
- the starter grant service must be available to the MCP server

## Typical workflow

### Recommended flow

1. Call `request_starter_grant`.
2. Check that the result status is `claimed`.
3. Confirm the wallet balance through a transaction or balance tool.

### Manual flow

1. Call `get_starter_grant_status`.
2. If eligible, call `get_starter_grant_challenge`.
3. Answer the lightweight challenge.
4. Call `claim_starter_grant`.

## Tool reference

### `request_starter_grant`

Runs the full challenge and claim sequence in one call.

### `get_starter_grant_status`

Returns one of:

- `eligible`
- `challenge_pending`
- `claimed`

### `get_starter_grant_challenge`

Returns the challenge prompt, challenge ID, claim payload, and expiry.

### `claim_starter_grant`

Signs the claim payload with the configured wallet and submits the claim.

## Common failures

- the wallet already claimed its one-time grant
- the challenge expired
- the starter grant service is unreachable
- the local install ID state is inconsistent

## Important notes

- the grant is one-time per wallet
- the challenge is intentionally simple
- the install ID is a soft deduplication signal, not a trustless identity primitive
- after claiming, the wallet should have enough COTI for initial messaging activity
