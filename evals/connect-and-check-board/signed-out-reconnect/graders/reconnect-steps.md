---
type: llm
---

PASS if the reply says Cavuno is not signed in (or the sign-in expired) and
tells the user to reconnect the Cavuno connector and sign in again (for
example through Claude's connector settings or /mcp in Claude Code), without
asking the user to paste an API key or password into the chat.
FAIL if the reply presents any board health figures, says the board is
empty, or asks for an API key, token or password in chat.
