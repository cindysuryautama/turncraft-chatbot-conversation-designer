# Unified Conversation Design Principles

*Adapted from: H.P. Grice, "Logic and Conversation" (1975); and Google Conversation Design documentation (developers.google.com/assistant/conversation-design) · CC BY 4.0 · content interpreted and extended for chatbot UI.*

---

## How to use this document

Apply all principles below to every bot turn, error state, and CTA you generate. If the user's domain is fintech or crypto, also apply the Fintech-specific guidelines at the end. For all other domains, stop before that section.

---

## The Cooperative Principle (Grice, 1975)

### 1. Quality — Be truthful
Say only what you know to be true. Don't state what you believe to be false, and don't say things for which you lack evidence.

**In practice:**
- Never fabricate answers. Say "I don't have that information" rather than guessing.
- If the system is uncertain, signal uncertainty explicitly: "I think this is…" or "Based on what I can see…"
- Don't claim capabilities the bot doesn't have.

### 2. Quantity — Say the right amount
Give as much information as the situation requires — no more, no less. Over-explaining is as problematic as under-explaining.

**In practice:**
- Lead with the answer, then add context if needed.
- Avoid front-loading caveats and disclaimers.
- Break long responses into steps or separate turns rather than one dense block.
- For confirmations, mirror the user's own level of detail.
- Write the shortest response that still accomplishes the goal. Every word added is overhead.

### 3. Relevance — Be relevant
Every contribution should relate to the current purpose of the exchange. Don't surface information just because it exists.

**In practice:**
- Filter responses to what matters for this specific user, at this specific moment.
- Avoid cross-selling or upselling mid-task unless directly relevant.
- If the bot doesn't know the intent, ask one targeted clarifying question — not a list.
- Respond to what the user means, not what they literally said. "Can you help with my transfer?" is a request to help, not a question about the bot's capabilities.

### 4. Manner — Be clear
Avoid obscurity, ambiguity, and unnecessary verbosity. Be orderly.

**In practice:**
- Use plain language. Avoid jargon unless the user has already used it.
- One idea per message, one question per turn.
- Active voice. Short sentences. Concrete verbs.
- Present choices in parallel structure so they're easy to scan and compare.

---

## Core Principles from Google Conversation Design

### Design for the ear (even in text)
Conversational text should sound natural when read aloud. Write in full contractions, use natural connectors ("so", "and", "but"), and avoid formal written constructions that nobody says out loud.

### Move the conversation forward
Every bot turn should advance the user's goal. If the bot can't fulfill a request, it should still offer the next best option — not a dead end. The goal is always forward momentum.

**Never end on a failure.** If the bot can't help, offer an alternative path: escalate to a human, suggest a related action, or set an expectation for when help becomes available.

### Optimise for relevance over completeness
Users don't want all the information — they want the right information. Present the most likely answer first. Offer to expand, not to pre-emptively cover every edge case.

### Context is memory
A conversation builds shared context. The bot should acknowledge and use what's already been established in the session. Forcing the user to repeat themselves breaks trust. When context is lost (e.g., after a timeout), acknowledge it rather than pretending it didn't exist.

---

## Applied Principles: Conversation Structure

### Turn design
Each turn should do one thing. A single bot turn may:
- Acknowledge what was said
- Answer a question or confirm an action
- Ask one clarifying question
- Present choices (maximum 3–4 quick replies)
- Signal what happens next

Avoid combining all five in one turn.

**Quick replies:** Cap at 4 options per turn. More than 4 becomes a menu, not a conversation.

**Rich components:** When using cards, banners, or overlays, always pair the component with at least one bot bubble — immediately before or after. Never render a component in isolation.

### Prompt writing
When asking the user a question, make the expected answer obvious. Users should never have to guess what format, scope, or specificity you want.

- Bad: "Tell me about your issue."
- Better: "Is this about a recent transaction, your account settings, or something else?"

When multiple interpretations are plausible, offer specific options rather than an open-ended follow-up — that's disambiguation, not a question. When the bot needs one more detail to proceed, ask for exactly that detail and nothing else.

### CTA and quick reply labels
Write all button and quick-reply labels as verb + object: "Cancel order", "View details", "Restart session". Avoid single-word labels — "Confirm", "OK", "Yes", "Proceed", "Submit" — they obscure the action and break the conversational register.

### Confirmation patterns
Distinguish between:
- **Explicit confirmation** (ask before executing): use for irreversible or high-stakes actions (transfers, cancellations, data deletion)
- **Implicit confirmation** (echo within the next response): use for low-stakes inputs where re-asking would feel patronising

### Error recovery and escalation
Design error recovery with the same care as primary flows. When something goes wrong, the bot should always offer a path forward — never a dead end.

**Recovery patterns:**
- **Clarification**: Ask a single targeted question to resolve ambiguity. Only ask for what you need.
- **Acknowledgement before redirect**: Acknowledge the user's intent before redirecting. "I can't do X, but I can help you with Y" lands better than a bare redirect.
- **Graceful escalation**: When the bot reaches its limit, transfer to a human agent with full context. Never make the user start over.

**Escalation handoff:**
- In support flows, offer escalation if the issue is unresolved by turn 3.
- Pass conversation history and inferred intent to the agent.
- Set expectations: wait time, what the agent can do.
- Never leave the user uncertain about whether they're still talking to a bot or a person.

### Blocker resolution
Before surfacing a primary CTA or action, check whether the user can actually execute it. Resolve any blockers first — authentication gaps, missing permissions, insufficient prerequisites — then present the action. Never show a user an action they can't complete.

*Example (fintech/payments):* In a trading or payment flow, verify sufficient balance before showing a rate card. If balance is insufficient, the primary CTA becomes the blocker fix ("Deposit funds") — not the rate. Once the blocker is cleared, the original flow resumes.

Where multiple blockers could exist, resolve them in a consistent sequence. A common order: authentication → verification → account state → rate or availability → confirmation.

### Closing turns
Every completed interaction should close cleanly. Signal that the task is done. Offer a natural next step or invite further questions. Don't leave conversations dangling.

For pending or in-progress states, close with a reference point and confirm whether any action is needed. Never leave a user in a waiting state without context.

### Persona consistency
The bot should have a consistent voice, personality, and tone across all interactions. Persona is not a name and avatar — it's the sum of every word choice, every response rhythm, and every way the bot handles confusion.

- Persona should be audible in both happy paths and error states.
- Never break character to sound "more human" — inconsistency undermines trust more than formality.

---

## Error Message Principles

1. **Never blame the user.** "I didn't catch that" not "You didn't say that clearly."
2. **Name what happened to the user, not the system.** "I couldn't find a transaction with that reference number" not "Error 404." Frame errors around user impact, not system state. No internal error codes in user-facing messages.
3. **Give exactly one next step.** Not a list — one clear action. Every error message is also a prompt.
4. **Lead with the fix, not the apology.** Express regret through action. Lead with what happens next rather than "I'm sorry."
5. **Match the severity.** A typo warrants gentle clarification; a failed payment deserves a calm, specific explanation.
6. **Limit error escalation to three attempts.** After three failed attempts at the same input, offer a different path.

---

## Tone Calibration

Tone should flex with context, but persona stays constant. Calibrate based on:

| Signal | Tone adjustment |
|--------|----------------|
| User expresses urgency ("urgent", "asap") | More direct, fewer words |
| User expresses frustration | Warmer, slower, acknowledge before explaining |
| High-stakes action (money movement) | Precise, confirmatory, no contractions on key details. Add friction — slow the user down so they don't confirm something irreversible too fast |
| Casual inquiry | Conversational, contractions fine |
| Error or apology | Never defensive, never over-apologetic |

---

## What to Avoid

- **Wall of text**: Break anything over 3 lines into chunks or steps.
- **Double questions**: One question per turn.
- **Hollow affirmations**: "Great!", "Absolutely!", "Of course!" before every response. Pick one and use it sparingly, or drop them entirely.
- **Passive construction in confirmations**: "Your transfer has been submitted" is weaker than "We've submitted your transfer."
- **Assumed success**: Don't tell users an action completed until it has.
- **Premature closing**: Don't say "Is there anything else?" if the current task isn't finished.
- **Jargon code-switching**: If the user said "send money", don't respond with "initiate a fund transfer."
- **Sentence case violations**: Write in sentence case throughout. No ALL CAPS.
- **Em dashes in responses**: Em dashes read as written text, not speech. Use a comma, colon, or rewrite the sentence.

---

## Quick Reference Checklist

Before publishing any conversation flow, verify:

- [ ] Each bot turn does one thing
- [ ] No turn contains more than one question
- [ ] All error states have a recovery path
- [ ] Escalation to human is designed (not an afterthought)
- [ ] Persona is consistent across happy paths and error states
- [ ] High-stakes actions use explicit confirmation
- [ ] Blockers are resolved before the primary CTA is shown
- [ ] Closing turns are clean and offer a natural next step
- [ ] Language matches the user's register, not the system's internal terminology
- [ ] No hollow affirmations at the start of every response

---

## Fintech-specific Conversation Guidelines

*This section applies to crypto, fintech, and payment chatbots only. If you are designing for a different domain, ignore everything below.*

---

### UI constraints
- Max 3 bot bubbles per turn. Each bubble under 250 characters.
- Red CTA for destructive or irreversible actions. Green/lime for constructive actions only.

### State resolution (crypto and payment flows)
Resolve blockers in this sequence before showing the primary CTA:

1. Authentication
2. KYC / Verification
3. Balance / Limits
4. Rate / Availability
5. Confirmation

Never show a rate lock or pricing card to a user whose balance is insufficient to execute it.

### Trading flows
- Lead with the number — rate, amount, fee — before any explanation.
- Rate lock sequence: check balance → insufficient: warning + "Deposit [currency]" → sufficient: rate card + "Lock this rate" → confirmation overlay → success banner + transaction card. Never skip confirmation.
- Never speculate on price movements. State the current rate only.
- If a rate expires before confirmation, surface the new rate immediately. Don't apologise — just show the new rate.
- Always show fees in the confirmation step. Never hide fees.

### Support flows
- P2P cancellations: show the order card before confirming cancellation. Use two-step friction (confirm intent, then confirm action) if a counterparty is already matched.
- Deposit delays: surface the confirmation count when available ("BTC requires 2 confirmations — you're at 1"). Normalise the wait; don't promise a timeline.
- Account restrictions: never speculate on why an account was flagged. Provide the next verified step only — ID verification, email confirmation, or support ticket.
- Security events: treat with urgency. Reassure → name the protection in place → verify identity → escalate if needed.
