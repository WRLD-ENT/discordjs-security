# discordjs-security

Security utilities for **discord.js v14+**.

This package focuses on safe output (no accidental pings), secret redaction, cooldowns/rate limiting,
permission/role guards, content filtering, and secure logging.

## Install
```bash
npm i discordjs-security
```

## Quick start

```ts
import {
  safeReply,
  safeSend,
  redact,
  createCooldownManager,
  guardGuildOnly,
  guardPermissions,
  filterContent,
  createSecureLogger
} from "discordjs-security";

// Example: safe reply (no pings)
await safeReply(interaction, { content: "Hello!", ephemeral: true });

// Example: cooldown (3 uses per 10 seconds, per-user per-guild)
const cd = createCooldownManager();
const hit = cd.hit({ userId: interaction.user.id, guildId: interaction.guildId, channelId: interaction.channelId }, {
  durationMs: 10_000,
  limit: 3,
  key: "user:guild"
});
if (!hit.ok) return safeReply(interaction, { content: `Slow down. Try again in ${hit.retryAfterMs}ms`, ephemeral: true });

// Example: content filtering
const decision = filterContent(message.content, { blockInvites: true, blockEveryoneMentions: true });
if (!decision.ok) {
  await safeReply(interaction, { content: `Blocked: ${decision.reason}`, ephemeral: true });
}

// Example: secure logger
const log = createSecureLogger({ extraSecrets: [process.env.DISCORD_TOKEN ?? ""] });
log.info("Starting with token=%s", process.env.DISCORD_TOKEN);
```
