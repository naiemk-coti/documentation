# Agent skills (Cursor / compatible clients)

These folders are **real** agent skills: each is a directory with a `SKILL.md` that includes YAML frontmatter (`name`, `description`) so tools like Cursor can discover and load them.

They document workflows on top of the **private messaging MCP** surface: messaging, rewards, and starter grant.

## Available skills

| Skill | Path |
|-------|------|
| COTI Private Messaging | [coti-private-messaging/SKILL.md](coti-private-messaging/SKILL.md) |
| COTI Rewards Management | [coti-rewards-management/SKILL.md](coti-rewards-management/SKILL.md) |
| COTI Starter Grant | [coti-starter-grant/SKILL.md](coti-starter-grant/SKILL.md) |

## Prerequisites

Before these skills are useful, the following pieces need to exist:

- a configured COTI wallet
- an AES key for private operations
- access to the private messaging MCP server
- native COTI for gas, or access to the starter grant flow

## Use in Cursor-compatible clients

If your agent runtime supports Cursor-style `SKILL.md` discovery, copy the skill folders into the repo where that agent runs:

```bash
mkdir -p .cursor/skills
cp -R /path/to/private-messaging/skills/coti-private-messaging .cursor/skills/
cp -R /path/to/private-messaging/skills/coti-rewards-management .cursor/skills/
cp -R /path/to/private-messaging/skills/coti-starter-grant .cursor/skills/
```

For a personal install across projects, copy the same folders under `~/.cursor/skills/` instead.

## Use after install

After the folders are in place, call the skill by name in the prompt. Keep the request explicit.

Examples:

```text
Use the coti-private-messaging skill to send a private message to 0xabc... with the plaintext "hello from COTI".
```

```text
Use the coti-rewards-management skill to check my current epoch and pending rewards.
```

```text
Use the coti-starter-grant skill to fund this new wallet so it can start messaging.
```

## How to use this section

- In Cursor-compatible clients: install the folders under `.cursor/skills/`, then call the skill by name in the prompt.
- In standalone agents: treat the `SKILL.md` bodies as prompt-ready workflow docs and inject them through your own runtime.
