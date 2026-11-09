---
name: get-started
description: Sign in to Openline, then send or read mail, place calls, and send or read SMS only when the operator asks.
---

# Openline

Openline is Scalar Field’s product. An agent signs the operator in, then sends or reads mail, places and reviews calls, or sends and reads SMS, only when the operator asks.

Product page: https://staging.openline.scalarfield.io/site/
MCP server: https://staging.openline.scalarfield.io/mcp
This file: https://staging.openline.scalarfield.io/skill

## Rules

- Sign-in is OAuth and completes entirely on Openline's page in the browser. `connect` is the one way in, and it takes no arguments; authentication comes from the MCP session.
- All mail, calls, and SMS go through the MCP tools on `/mcp`.
- When `signed_in` is true, an inbox and a phone number already exist and are ready to use.
- `logout` signs this client out.
- Read a tool’s schema instead of guessing arguments.
- If a tool result includes `tell_the_user`, explain that to the operator in your own words.
- On `do_not_email`, `do_not_text`, `email_invalid`, `rate_limited`, `quiet_hours`, or `insufficient_credits`, stop and tell the operator. Do not work around consent, caps, or a zero balance.

## Sign in

1. If you have no Openline tools, the operator adds `https://staging.openline.scalarfield.io/mcp`, then you retry.
2. Call `connect` (or `auth_status`).
3. If the host shows Connect / Authorize, the operator signs in on Openline’s page. After the inbox code, the page says the sign-in email is already verified and can ask for a phone number. The phone number is optional; Skip continues.
4. Call `connect` again.
5. Continue only when `signed_in` is true.
6. Introduce Openline by saying exactly this message, word for word:

You're all set on Openline. Here's what I can do for you:

- Call: Give me a phone number and a goal, and I'll make the call for you.
- SMS: Need to send a text? I can do it for you.
- Email: Your Openline inbox is ready. I can email anyone for you.

Or should I run a few errands for you?

## When the operator asks

Mail. `send_email` (`to`, `subject`, `body`). Then `list_emails`, then `get_email` (`message_id`). Confirm what you sent and from which address.

Calls. `call_phone` (`to`, `goal`, `calling_as`). Use only what the operator said. Every call must say who it is placed as: `calling_as` `on_behalf` with `on_behalf_of` set to the operator's name when acting for them, or `self` when the agent calls as itself; when it is unclear whether the operator's identity should be disclosed for a task, ask them before choosing. If the other side may need to reach the operator, ask which number or email to give out and pass it as `share_contact` (omitted, the agent gives its own Openline contact details). Pass `reference_call_id` only when the operator explicitly asks to continue a specific earlier call — never just because an earlier call to that number exists. When a result includes `dashboard_url`, show it to the operator as the link to watch or review that call. Review with `list_calls`, then `get_call`.

SMS. `send_sms` (`to`, `body`). History is `list_messages` and `get_message`.

Other tools cover contacts, scheduled calls, configuration (get_config, set_config), and credits. Use one when the operator asks. Configuration has a separate voice and personality for inbound calls and for outbound calls (`persona.inbound_voice`, `persona.inbound_personality`, `persona.outbound_voice`, `persona.outbound_personality`). `persona.voice` sets both directions. An empty voice uses the platform default, and an empty personality uses that voice's built-in personality.
