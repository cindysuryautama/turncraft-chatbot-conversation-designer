# Design System: Material Design 3 (Material You)

*Adapted from Material Design 3 by Google — [material.io/design](https://material.io/design), licensed CC BY 4.0. Content has been interpreted, reorganised, and extended with chatbot-specific guidance. Not a verbatim reproduction. Note: material.io is fully JS-rendered; verify token values and specs directly at m3.material.io before implementation.*

---

## Overview

Material Design 3 (M3) is Google's current design system. It is component-based, token-driven, and built for Android and cross-platform apps. While M3 is not purpose-built for chatbot UI, its components map naturally to conversational patterns when applied correctly. M3 is the right choice when: the product targets Android or cross-platform mobile, the team already uses M3 or Google's Jetpack Compose, or a general-purpose design system is preferred over an AI-native one.

---

## Component Mapping for Chatbot UI

### 1. Message Bubbles → `Surface` + `Card` (Tonal)

M3 does not have a dedicated chat bubble component. The canonical approach is a tonal `Surface` or `Card` component with shaped corners.

**Bot message:**
- Background: `Surface` at elevation level 1 or `Secondary Container` colour
- Corner shape: `ShapeDefaults.ExtraLarge` (28dp), trailing-bottom corner `Small` (4dp) to indicate directionality
- Typography: `Body Large` or `Body Medium`

**User message:**
- Background: `Primary Container` colour
- Corner shape: `ShapeDefaults.ExtraLarge` (28dp), leading-bottom corner `Small` (4dp)
- Alignment: right-aligned

**Timestamp / sender label:**
- Typography: `Label Small`
- Colour: `On Surface Variant`

**Usage notes:**
- Use `Surface` for simple text replies
- Use `Card` (elevated or tonal) when the message contains structured content (image, list, action)
- Never use `Filled Card` for both bot and user — differentiate with colour, not elevation alone

---

### 2. Quick Replies / Suggestion Chips → `Suggestion Chip`

M3 `Suggestion Chip` is the canonical component for quick-reply options in a conversation.

**Anatomy:**
- Container: outlined (default) or tonal-filled
- Label: `Label Large` typography
- Optional leading icon (16dp)
- Height: 32dp (compact) or 40dp (default)

**Behaviour:**
- Chips disappear or become disabled after the user selects one
- Display in a horizontal scrollable row below the bot message
- Maximum 4 chips visible without scrolling; show 3 if a 4th is cut off

**When to use:**
- Binary choices: Yes / No, Confirm / Cancel
- Common action shortcuts: "Check balance", "View history", "Talk to an agent"
- Follow-up prompts after an informational response

**When not to use:**
- When there are more than 6 options (use a list instead)
- When options are variable or dynamically unknown at design time

---

### 3. Typing Indicator / Loading State → `CircularProgressIndicator` (Indeterminate)

M3 does not have a built-in typing indicator. The standard approach is:

**Option A — Inline indeterminate:**
- Small `CircularProgressIndicator` (indeterminate, 20dp) inside a bot message bubble
- Replaces text content until the response is ready

**Option B — Three-dot animation (custom):**
- Three 6dp dots animating in sequence
- Built with `Animatable` or `InfiniteTransition` in Compose
- Contained in a `Surface` bubble identical to the bot message style

**Usage notes:**
- Always show a loading state when response latency exceeds ~600ms
- Never show a spinner that resolves in under 300ms (it flickers)

---

### 4. Disambiguation / Selection Lists → `List Item` (Three-line)

**When to use disambiguation vs. quick replies:**
- Use disambiguation (List Items) when there are 3 or more paths with meaningfully different outcomes — the user needs to understand each option before choosing.
- Use Suggestion Chips (quick replies) for 2–5 lightweight follow-up shortcuts where the options are already self-evident.
- Never mix both in the same turn.

When the bot needs the user to choose from a set of results or options, use M3 `List Item` components.

**Anatomy:**
- Leading: icon or avatar (optional)
- Headline: option title (`Body Large`)
- Supporting text: description, 1–2 lines (`Body Medium`, `On Surface Variant`)
- Trailing: radio button, checkbox, or navigation arrow

**Variants for conversation:**
- **Single-select disambiguation**: Radio button trailing, confirm button below list
- **Multi-select**: Checkbox trailing, confirm button below list
- **Navigation list**: No trailing control, tap navigates (e.g., "Choose a category")

**Container:**
- Wrap in a tonal `Surface` at elevation level 1 inside the message stream
- Use a `Divider` between items

---

### 5. System Notices / Banners → `Banner` or `Snackbar`

**Banner** — persistent, low-disruption notices:
- Full-width, sits below the app bar or at the top of the chat container
- 1–2 lines of text + up to 2 text buttons
- Use for: maintenance notice, account status, verification required
- Dismiss: explicit button tap (not auto-dismiss)

**Snackbar** — transient, action feedback:
- Full-width at the bottom of the screen
- 1 line of text + optional single action button
- Auto-dismiss after 4s (or 10s if action present)
- Use for: message sent confirmation, session timeout warning, copy-to-clipboard confirmation
- Do not use for errors that require user action

**Semantic intent mapping** — choose the right banner type based on what triggered it:

| Intent | M3 component | Trigger condition |
|--------|-------------|-------------------|
| Warning | `Banner` | Pre-emptive caution before an action — user hasn't acted yet |
| Info | `Banner` | Neutral system state the user should be aware of |
| Success | `Snackbar` | After an action completes — never before |
| Error | `Banner` (persistent) | Reactive failure — action did not complete, user must act |

Always pair a banner with a bot bubble. Never render a banner as the sole response turn.

---

### 6. Input Field → `Outlined TextField` or `Filled TextField`

**Filled TextField** (recommended for chat):
- Background: `Surface Variant`
- Floating label or hint text
- Bottom line indicator (active state: `Primary` colour)
- Trailing: send icon button (`Icon Button`, `Primary` tint)

**Outlined TextField** (alternative for lighter backgrounds):
- 4dp radius border
- More separation from background

**Multi-line behaviour:**
- Grows up to 4 lines, then scrolls
- Send button remains fixed-position (not inline with overflow text)

**Accessory bar:**
- Attachment, emoji, or voice input icons sit in a row above or alongside the text field
- Use `Icon Button` (standard, 48dp hit target) for each accessory

---

### 7. Confirmation Dialogs → `Dialog` (Alert)

For high-stakes confirmations (money movement, account changes, data deletion):

**Anatomy:**
- Title: `Headline Small` — name the action ("Confirm transfer")
- Body: 1–3 lines of specifics (`Body Medium`)
- Buttons: two text buttons — dismissive left ("Cancel"), confirmatory right ("Confirm" or action verb)
- Confirmatory button: `Text Button` with `Primary` colour, or `Filled Button` for highest-stakes

**Destructive actions:** When the confirmation covers an irreversible or destructive action (delete, cancel an order, revoke access), use `error` colour on the confirmatory button to signal consequence. The dismiss button stays neutral.

**M3 rules:**
- Dialogs should always have a dismiss option
- Button labels should be action verbs, not "OK" / "Yes"
- Never use a dialog for non-blocking information — use a `Snackbar` or `Banner`
- Only trigger a dialog after the user has already expressed intent — not at the discovery stage

---

### 8. Escalation / Agent Handoff → `Navigation Bar` item or `FAB` (Extended)

When offering human handoff:

**Option A — Extended FAB:**
- `Extended FAB` with icon (e.g., headset icon) and label "Talk to an agent"
- Fixed position, bottom-right of chat container
- Appears after 2+ unresolved turns or explicit user request

**Option B — Inline message CTA:**
- Bot message with a `Filled Tonal Button` embedded at the bottom of the bubble
- "Connect to support →"

**Never:**
- Use a plain hyperlink for escalation (low affordance on mobile)
- Hide escalation behind a menu when the user is clearly frustrated

---

## Token Reference for Conversation UI

| Role | M3 Token | Usage |
|------|----------|-------|
| Bot bubble background | `md.sys.color.secondary-container` | Background of bot messages |
| User bubble background | `md.sys.color.primary-container` | Background of user messages |
| Input background | `md.sys.color.surface-variant` | Filled text field background |
| Hint / timestamp | `md.sys.color.on-surface-variant` | Secondary text, metadata |
| Error state | `md.sys.color.error` | Error messages, failed actions |
| Success / positive | `md.sys.color.tertiary` | Confirmations, success states |
| Send button active | `md.sys.color.primary` | Icon button tint when input is non-empty |

---

## Shape Reference

| Component | Corner radius |
|-----------|---------------|
| Bot message bubble | ExtraLarge (28dp) except trailing-bottom: 4dp |
| User message bubble | ExtraLarge (28dp) except leading-bottom: 4dp |
| Suggestion chip | Full (circular, 50%) |
| Dialog | ExtraLarge (28dp) |
| Text field | ExtraSmall (4dp) top corners only (filled) |
| Snackbar | ExtraSmall (4dp) |

---

## Typography Scale in Conversation

| Use | M3 Style | Size |
|-----|----------|------|
| Message body | `Body Large` | 16sp |
| Supporting text | `Body Medium` | 14sp |
| Chip label | `Label Large` | 14sp |
| Timestamp / metadata | `Label Small` | 11sp |
| Dialog title | `Headline Small` | 24sp |
| Error / status label | `Label Medium` | 12sp |

---

## Action-State Gating

Before surfacing a component, verify that the prerequisite state is met. Never show a component that implies readiness when a blocker exists.

- Don't show a confirmation dialog if a required input (balance, validation, KYC) hasn't been resolved.
- Don't show a success `Snackbar` until the action is confirmed server-side.
- Don't show a primary CTA inside a card until prerequisites are cleared — surface a `Banner` (warning) and a corrective action instead.
- Resolve blockers in order: authentication → eligibility → data readiness → confirmation → outcome.

---

## Cards — Structured Information Display

Use a card when a bot response contains structured, multi-field data that would be unreadable as prose. Cards make status and details scannable at a glance.

**When to use:**
- 3 or more distinct data points that belong together (e.g., order ID, status, amount, date)
- The user needs to review and verify before acting
- A confirmed system state needs to persist visually in the thread

**When not to use:**
- A single data point — show it inline in the bubble
- Listing multiple separate items — use a `List Item` group instead
- Before data is confirmed — don't show a card with placeholder or loading values

**Generic card anatomy** (map to M3 `Elevated Card` or `Tonal Card`):
- **Header** — subject name or title (`Title Medium`)
- **Key-value rows** — 2–5 labelled fields (`Body Medium` label, `Body Medium` value)
- **Status badge** — optional, use M3 colour tokens (`tertiary` for positive, `error` for failed)
- **Action** — optional single `Text Button` or `Filled Button` at the bottom

**Usage rules:**
- One card per turn. Never stack multiple cards in the same response.
- Always preceded by a bot bubble that contextualises the card.
- Every field must be populated with real data — a card with blank fields is invalid output.
- Don't repeat card content in the bot bubble — the card is the detail, the bubble is the context.

---

## Key Constraints and Gotchas

- M3 has no native chat bubble or typing indicator component — both require custom implementation
- Elevation system changed significantly from M2; don't mix M2 elevation tokens with M3 colour tokens
- `Dynamic Color` (Material You personalisation) can create unpredictable bot/user bubble colour contrast — always verify accessible contrast ratios against wallpaper-generated palettes
- `Suggestion Chip` is distinct from `Filter Chip`, `Input Chip`, and `Assist Chip` — use `Suggestion Chip` specifically for bot-generated options
- On Android, `AlertDialog` in Compose follows M3 specs automatically; on other platforms, apply M3 shape + colour tokens manually
