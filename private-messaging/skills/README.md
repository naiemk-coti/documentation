# Agent skills (Cursor / compatible clients)

These folders are **real** agent skills: each is a directory with a `SKILL.md` that includes YAML frontmatter (`name`, `description`) so tools like Cursor can discover and load them.

They document workflows on top of the **private messaging MCP** surface: setup, messaging, rewards, and optional grant troubleshooting.

## Available skills

| Skill | Path |
|-------|------|
| COTI Private Messaging | [coti-private-messaging/SKILL.md](coti-private-messaging/SKILL.md) |
| COTI Rewards Management | [coti-rewards-management/SKILL.md](coti-rewards-management/SKILL.md) |
| COTI Starter Grant | [coti-starter-grant/SKILL.md](coti-starter-grant/SKILL.md) |

Use `coti-private-messaging` as the default skill. It now covers first-run setup, starter-grant fallback, and send/read flows. Keep `coti-starter-grant` only if you want a grant-only troubleshooting reference.

## Prerequisites

Before MCP-based messaging works, the following pieces need to exist:

- a configured COTI wallet
- an AES key for private operations
- access to the private messaging MCP server
- native COTI for gas, or access to the starter grant flow

For the shortest first-run path, install, initialize, and send with one terminal command:

```bash
mkdir -p coti-private-message && cd coti-private-message && npm init -y && npm install @coti-io/coti-sdk-private-messaging @coti-io/coti-ethers dotenv && npx coti-private-messaging-send --init --to 0xabc... --text "hello from COTI"
```

If you prefer separate setup and send, run `npx coti-private-messaging-init` first and then `npx coti-private-messaging-send`.

## Use in Cursor-compatible clients

If your agent runtime supports Cursor-style `SKILL.md` discovery, copy the skill folders into the repo where that agent runs:

```bash
mkdir -p .cursor/skills
cp -R /path/to/private-messaging/skills/coti-private-messaging .cursor/skills/
cp -R /path/to/private-messaging/skills/coti-rewards-management .cursor/skills/
```

Optional grant-only reference:

```bash
cp -R /path/to/private-messaging/skills/coti-starter-grant .cursor/skills/
```

For a personal install across projects, copy the same folders under `~/.cursor/skills/` instead.

## Use after install

After the folders are in place, call the skill by name in the prompt. Keep the request explicit.

Examples:

Direct terminal send after setup:

```bash
npx coti-private-messaging-send --to 0xabc... --text "hello from COTI"
```

```text
Use the coti-private-messaging skill to check whether this workspace is ready for private messaging, finish setup if needed, and send a private message to 0xabc... with the plaintext "hello from COTI".
```

```text
Use the coti-rewards-management skill to check my current epoch and pending rewards.
```

`coti-starter-grant` is optional when you want to inspect or debug the grant flow directly rather than using the default setup path in `coti-private-messaging`.

## How to use this section

- In Cursor-compatible clients: install the folders under `.cursor/skills/`, then call `coti-private-messaging` first for setup and messaging.
- In standalone agents: treat the `SKILL.md` bodies as prompt-ready workflow docs and inject them through your own runtime.
