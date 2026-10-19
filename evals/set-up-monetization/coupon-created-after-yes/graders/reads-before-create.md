---
type: tool_order
before:
  tool: mcp__plugin_cavuno_cavuno__read
  input_match: '/coupons|/billing/connect|/plans'
after:
  tool: mcp__plugin_cavuno_cavuno__create
  input_match: '/coupons'
---
