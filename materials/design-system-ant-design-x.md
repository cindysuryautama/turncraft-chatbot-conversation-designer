# Design System: Ant Design X

*Adapted from Ant Design X by Ant Group — [x.ant.design](https://x.ant.design), MIT licensed. Content has been interpreted, reorganised, and extended with chatbot-specific guidance. Not a verbatim reproduction. Spot-checked against official API docs May 2026.*

---

## Overview

Ant Design X (ANT-X) is a component library built specifically for AI interaction interfaces. It extends the Ant Design ecosystem with components that address the unique patterns of conversational AI: streaming text, multi-turn dialogue, thinking chains, suggested prompts, and agent actions. ANT-X is the right choice when: building an AI-first product (chatbot, copilot, agent UI), the team is already on the Ant Design ecosystem, or you need production-ready components for streaming and agentic interactions rather than adapting a general-purpose system.

---

## Core Components

### 1. `Bubble` — The Message Bubble

The foundational unit of conversation. Renders a single message from either the user or the assistant.

**Key props:**
- `content` — the message text (supports plain string, number, or React node)
- `contentRender` — custom content renderer — use for Markdown, code blocks, or rich structured content inside a bubble
- `placement` — `'start'` (left, default) | `'end'` (right, for user messages)
- `avatar` — slot — bot icon, user avatar, or initials
- `loading` — boolean — shows animated typing indicator inside the bubble
- `loadingRender` — custom loading content renderer
- `typing` — `boolean | { effect, step, interval }` — streaming animation effect
- `streaming` — boolean — tells Bubble the content is still being streamed; prevents `onTypingComplete` from firing prematurely
- `shape` — `'default'` | `'round'` | `'corner'` — bubble corner style
- `variant` — `'filled'` (default) | `'borderless'` | `'outlined'` | `'shadow'`
- `header` — slot — sender name or timestamp above the message
- `footer` — slot — actions or metadata below the message text
- `footerPlacement` — `'outer-start'` (default) | `'outer-end'` | `'inner-start'` | `'inner-end'` — controls whether footer renders inside or outside the bubble
- `extra` — slot — sidebar content alongside the bubble
- `editable` — boolean — allows the user to edit message content inline

Note: `role` is **not** a Bubble prop. Role (e.g. `'user'`, `'assistant'`) is a field on each item in `Bubble.List`, used to apply default configurations via the `role` map on Bubble.List.

**Typing animation:**
```
typing={{ effect: 'typing', step: 6, interval: 100 }}
```
`effect` defaults to `'fade-in'`; use `'typing'` for the typewriter reveal. `step` defaults to 6 characters per interval; `interval` defaults to 100ms. Use `step: 1` for slower reveals, `step: 10+` for fast-streaming models.

**Streaming vs typing:**
Use `loading={true}` while waiting for the first token. Once content starts arriving, set `streaming={true}` alongside `typing` — this prevents `onTypingComplete` from firing multiple times during unstable stream input. Set `streaming={false}` only when the stream is fully complete.

**Design notes:**
- `variant="borderless"` works well for assistant messages when the background differentiates roles
- `footer` slot is the canonical location for thumbs-up/down feedback, copy button, and regenerate CTA
- `footerPlacement="inner-end"` places actions inside the bubble at the bottom-right — useful for compact layouts

---

### 2. `Bubble.List` — The Conversation Thread

Renders an ordered list of `Bubble` components from a message array, with automatic scroll-to-bottom behaviour.

**Key props:**
- `items` — array of message objects with `key` (required), `role` (required), `content`, and optionally `status` (`'local'` | `'loading'` | `'updating'` | `'success'` | `'error'` | `'abort'`)
- `autoScroll` — boolean (default `true`) — auto-scrolls to the latest message unless the user has scrolled up
- `role` — `Record<string, BubbleProps>` — define default props per role (avatar, variant, placement) so individual items don't need to repeat them

**`role` map pattern (recommended):**
```javascript
const role = {
  assistant: {
    placement: 'start',
    avatar: { icon: <BotIcon />, style: { background: '#f0f0f0' } },
    variant: 'borderless',
  },
  user: {
    placement: 'end',
    variant: 'filled',
  },
};
```

This keeps the `items` array clean — each item only needs `key`, `role`, and `content`. Final config priority: `items` props > `role` map > `Bubble.List` semantic styles.

**Typing state in list:**
Pass `loading: true` or `streaming: true` in the last item of `items` while the response is arriving. When the stream completes, remove `streaming` and set the final `content`.

**Design notes:**
- `autoScroll` disengages when the user scrolls up (reading history); it re-engages when they scroll back to the bottom
- Do not set a fixed height directly on `Bubble.List` — set a fixed height on its parent with `display: flex; flex-direction: column` instead. This keeps content top-aligned when the thread is short.

---

### 3. `Prompts` — Suggested Actions and Quick Replies

**When to use Prompts (chip mode) vs. disambiguation:**
- Use chip mode for 2–5 lightweight follow-up shortcuts where options are self-evident. Chips disappear after selection.
- Use card/vertical mode when there are 3+ paths with meaningfully different outcomes — the user needs to read and understand each option before choosing.
- Never present both in the same turn.

Renders a set of prompt suggestions — either at conversation start (onboarding prompts) or as contextual follow-ups after a bot message.

**Key props:**
- `items` — array of `PromptProps`:
  - `key` — unique identifier
  - `label` — display text (string or React node)
  - `description` — optional supporting text (shown in card variant)
  - `icon` — optional icon
  - `disabled` — boolean
  - `children` — nested `PromptProps[]` — supports grouped/hierarchical prompt structures (e.g. category > sub-prompts)
- `title` — optional section label ("What can I help with?")
- `onItemClick` — callback `(info: { data: PromptProps }) => void`
- `wrap` — boolean — whether prompt chips wrap to multiple lines (default `false`)
- `vertical` — boolean — stack items vertically (card layout) instead of horizontal chips (default `false`)
- `fadeIn` / `fadeInLeft` — boolean — entrance animation for prompts appearing after a bot message

**Two layout modes:**

**Chip mode** (horizontal, `wrap: true`):
- Compact quick-reply strips
- Use after a bot message when offering 3–5 follow-up options
- Items render as pill-shaped buttons

**Card mode** (`vertical: true`):
- Larger cards with icon + label + description
- Use at conversation start for onboarding ("What would you like to do today?")
- Suits 3–4 top-level intents

**Design notes:**
- Chips disappear after selection — update the `items` array to empty or swap to the next step
- Use `disabled` for options the user doesn't qualify for (don't hide them — show disabled state with a tooltip explaining why)
- Card mode with icons is the preferred layout for first-load empty state

---

### 4. `Welcome` — Conversation Start / Empty State

A full-page or panel component for the pre-conversation empty state. Combines a greeting, description, and initial prompt suggestions.

**Key props:**
- `icon` — bot icon or product logo
- `title` — greeting headline ("Hi, I'm your assistant")
- `description` — 1–2 sentence explanation of bot capabilities
- `prompts` — accepts a `Prompts` component directly (the two are designed to compose)
- `extra` — slot for supplementary CTAs or links

**Composition pattern:**
```jsx
<Welcome
  icon={<BotIcon />}
  title="What can I help you with today?"
  description="I can help with account queries, transfers, and transaction history."
  prompts={
    <Prompts
      title="Try asking:"
      items={suggestedPrompts}
      onItemClick={handlePromptClick}
      vertical
    />
  }
/>
```

**Design notes:**
- Replace `Welcome` with `Bubble.List` the moment the first message is sent — don't animate it out, just swap the render
- Keep `title` to one short question or greeting
- `description` should set expectations, not list features — "I can help with X and Y" not "Features include: A, B, C, D"

---

### 5. `ThoughtChain` — Reasoning / Agent Step Visibility

Displays a collapsible chain of reasoning steps or agent actions taken before a final response. Designed for AI agents that perform multi-step work (tool calls, retrieval, computation).

**Key props:**
- `items` — array of `ThoughtChainItem`:
  - `key` — identifier
  - `title` — step label ("Searching transactions", "Calculating total")
  - `description` — optional detail text or output preview
  - `status` — `'pending'` | `'success'` | `'error'` — drives icon and colour
  - `icon` — optional custom step icon
  - `extra` — slot for duration, token count, or other metadata
- `collapsible` — boolean — allows user to expand/collapse the chain
- `size` — `'small'` | `'default'`

**Status icons (default):**
- `pending` — animated spinner
- `success` — checkmark (green)
- `error` — exclamation (red)

**Usage pattern:**
1. Render `ThoughtChain` with `status: 'pending'` steps as the agent works
2. Update each step to `success` as it completes
3. On final response, collapse the chain automatically (or leave expanded for transparency)

**Design notes:**
- Show `ThoughtChain` above the final `Bubble` response, not below it
- Default to collapsed after completion — users who want to inspect can expand
- Use `title` as a human-readable verb phrase: "Checking your recent transfers" not "FUNCTION_CALL: get_transactions"
- Never show `ThoughtChain` for simple single-turn responses — it adds noise

---

### 6. `Sender` — Chat Input

The message input component. Handles text entry, multi-line expansion, submission, and optional speech/attachment controls.

**Key props:**
- `value` — controlled input value
- `onChange` — text change handler
- `onSubmit` — submit handler (fires on Enter or send button click)
- `onCancel` — cancels in-progress streaming generation
- `onPasteFile` — callback for pasted files (use with `Attachments` for paste-to-upload)
- `loading` — boolean — shows cancel button instead of send when the bot is generating
- `placeholder` — hint text
- `allowSpeech` — boolean — shows microphone button (voice input)
- `submitType` — `'enter'` (default) | `'shiftEnter'` — controls whether Enter submits or creates a newline
- `prefix` — slot to the left of the input area
- `suffix` — slot for action buttons to the right of the input (send, cancel, speech). Default buttons are included automatically; replace with `suffix={false}` to remove them entirely
- `header` — collapsible panel above the input (use `Sender.Header` sub-component for full control)
- `footer` — slot below the input (e.g., "Powered by" note)
- `disabled` — boolean — prevents input during hard-blocked states
- `readOnly` — boolean — shows content without allowing edits

**Loading / cancel pattern:**
```javascript
// While bot is generating:
<Sender loading={true} onCancel={handleCancel} />
// Otherwise:
<Sender onSubmit={handleSubmit} />
```

The `loading` prop swaps the send icon for a stop/cancel icon automatically.

**Design notes:**
- Default `submitType` is `'enter'` — Enter submits, Shift+Enter creates a newline. This is the standard for chat interfaces. Only change to `'shiftEnter'` for document-editor contexts where Enter = newline is expected.
- Disable the sender during `ThoughtChain` steps if the agent cannot accept parallel input
- Use the `header` slot (via `Sender.Header`) to preview attached files above the input — don't stretch the input area itself for attachments

---

### 7. `Actions` / `BubbleActions` — Message-level Controls

Action controls that appear on hover or tap within a message bubble. Standard set: copy, thumbs up, thumbs down, regenerate.

**Placement:**
- Render in the `footer` slot of `Bubble`
- Use `Tooltip` from Ant Design for label on hover

**Standard action set for assistant messages:**
| Action | Icon | Behaviour |
|--------|------|-----------|
| Copy | `CopyOutlined` | Copies plain text of message to clipboard; shows `CheckOutlined` for 2s |
| Helpful | `LikeOutlined` | Positive feedback; toggles to `LikeFilled` |
| Not helpful | `DislikeOutlined` | Negative feedback; optionally opens feedback form |
| Regenerate | `SyncOutlined` | Re-runs the last user message; only show on the last assistant message |

**Design notes:**
- Show actions on hover (desktop) and on tap/hold (mobile) — don't render them persistently below every message
- Disable `Regenerate` if a generation is already in progress
- Feedback (like/dislike) should fire silently — no modal confirmation unless you need qualitative input

---

### 8. `Attachments` — File and Media Upload

Handles file and image attachment before sending.

**Key props:**
- `items` — array of attached file metadata
- `onChange` — file add/remove handler
- `accept` — MIME type filter
- `placeholder` — upload zone label

**Composition:**
Render in the `header` slot of `Sender` to show a preview strip of attached files above the text input.

---

## Component Composition: Full Chat Layout

```
┌─────────────────────────────────────┐
│  [Welcome / Bubble.List]            │  ← swaps on first message
│                                     │
│  [ThoughtChain] (when agent active) │
│                                     │
│  [Bubble.List]                      │
│    └─ Bubble (assistant)            │
│         └─ footer: [Actions]        │
│    └─ Bubble (user)                 │
│    └─ Bubble loading (streaming)    │
│                                     │
│  [Prompts] (contextual follow-ups)  │
├─────────────────────────────────────┤
│  [Sender]                           │
│    header: [Attachments preview]    │
│    actions: [attachment, voice]     │
└─────────────────────────────────────┘
```

---

## System Notices / Banners

Ant Design X does not ship a dedicated banner component. Use Ant Design's `Alert` component for in-conversation notices, placed inline within the message stream.

**Semantic intent mapping** — choose the right `Alert` type based on what triggered it:

| Intent | `Alert` type | Trigger condition |
|--------|-------------|-------------------|
| Warning | `type="warning"` | Pre-emptive caution before an action — user hasn't acted yet |
| Info | `type="info"` | Neutral system state the user should be aware of |
| Success | `type="success"` | After an action completes — never before |
| Error | `type="error"` | Reactive failure — action did not complete, user must act |

Always pair an `Alert` with a `Bubble` message above it. Never render an `Alert` as the sole response in a turn.

For success feedback that doesn't require user attention, prefer `message.success()` (Ant Design's global toast) over an inline `Alert`.

---

## Confirmation Pattern

Ant Design X does not include a confirmation component. Use Ant Design's `Modal.confirm()` or a custom `Modal` for high-stakes confirmations.

**Anatomy:**
- Title — name the action as an action verb ("Cancel order", "Delete account")
- Body — 1–3 lines of specifics: what will happen, what can't be undone
- Buttons — cancel (left, default style) and confirm (right, `Primary` or `Danger`)

**Destructive actions:** Use `danger` prop on the confirm button when the action is irreversible. This renders it in red. The cancel button stays neutral.

**Rules:**
- Only trigger after the user has expressed clear intent — never at the discovery stage.
- Button labels must be action verbs ("Delete account", not "OK").
- Never use a modal for non-blocking information — use `Alert` or `message` instead.

---

## Action-State Gating

Before surfacing a component, verify that the prerequisite state is met. Never show a component that implies readiness when a blocker exists.

- Don't trigger `Modal.confirm()` if a required input hasn't been resolved.
- Don't show a success `Alert` or `message.success()` until the action is confirmed.
- Don't surface a primary CTA until eligibility is confirmed — show a warning `Alert` and a corrective action first.
- Resolve blockers in order: authentication → eligibility → data readiness → confirmation → outcome.

---

## Cards — Structured Information Display

Use a card when a bot response contains structured, multi-field data that would be unreadable as prose. In Ant Design X, render cards as `Bubble` components with a custom `content` renderer, or use Ant Design's `Card` component placed inline in the message stream.

**When to use:**
- 3 or more distinct data points that belong together (e.g., order ID, status, amount, date)
- The user needs to review and verify before acting
- A confirmed system state needs to persist visually in the thread

**When not to use:**
- A single data point — show it inline in the bubble
- Listing multiple separate items — use `Prompts` in vertical mode or a list instead
- Before data is confirmed — never show a card with placeholder or loading values

**Generic card anatomy:**
- **Header** — subject name or title
- **Key-value rows** — 2–5 labelled fields
- **Status badge** — optional, use Ant Design's `Tag` component (`success`, `error`, `warning`, `processing` presets)
- **Action** — optional single `Button` at the bottom (`primary` or `default`)

**Usage rules:**
- One card per turn. Never stack multiple cards in the same response.
- Always preceded by a `Bubble` that contextualises the card.
- Every field must be populated with real data — a card with blank fields is invalid output.
- Don't repeat card content in the bubble — the card is the detail, the bubble is the context.

---

## Key Differences from General-Purpose Design Systems

| Pattern | Ant Design X native | M3 equivalent |
|---------|--------------------|--------------------|
| Streaming text animation | `Bubble typing` prop | Custom animation |
| Typing indicator | `Bubble loading` prop | Custom dots animation |
| Reasoning/agent steps | `ThoughtChain` | Custom timeline |
| Suggested prompts | `Prompts` component | `Suggestion Chip` (manual) |
| Cancel generation | `Sender loading + onCancel` | Custom icon swap |
| Conversation empty state | `Welcome` component | Custom empty state |
| Message feedback | `BubbleActions` pattern | Custom icon buttons |

---

## When to Choose Ant Design X vs Material Design 3

**Choose Ant Design X when:**
- Building an AI-first or agent-powered product
- Streaming responses and thought chains are core to the UX
- Team is on React and the broader Ant Design ecosystem
- You want production-ready AI-specific patterns, not adaptations

**Choose Material Design 3 when:**
- Building for Android or cross-platform with Jetpack Compose
- Chat is a secondary feature in a broader app
- Team already maintains an M3 design system
- Conversation patterns are simple (no streaming, no agentic steps)

---

## Installation Reference

```bash
npm install @ant-design/x
# or
yarn add @ant-design/x
```

Requires React 18+. Peer dependency on `antd` v5+ for shared components (Button, Avatar, Tooltip, etc.).

```javascript
import { Bubble, Prompts, Welcome, ThoughtChain, Sender } from '@ant-design/x';
```
