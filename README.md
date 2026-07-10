# Turncraft: Chatbot conversation designer

An AI-powered tool for designing governed chatbot conversations. Enter a user intent → map the full scenario space → generate phone-sim responses with your design system and guidelines baked in.

Built as a Claude artifact. Runs in [Cowork](https://claude.ai/download), Anthropic's desktop app.

---

## Why this exists

Chatbot responses are often configured by hand with no shared standard, leading to inconsistent tone, missed edge cases, and text-heavy dead ends. Turncraft grew out of fixing that: auditing real conversations, building a shared guidelines framework, and working with engineering to transform those guidelines into prompting guidance an LLM could actually follow consistently. This tool package the whole process so anyone can run it, test it, and evolve their own guidelines against a real scenario space instead of trusting them by inspection.

---

## What it does

- **Scenario mapping**: input a user intent and the tool expands the full scenario space (happy paths, error states, and edge cases)
- **Governed response generation**: guidelines apply in layers. Base conversation principles come first, domain-specific materials (design system, personas) add detail on top, and individual scenarios can override both when a case needs an exception
- **Phone simulator**: responses render in an interactive phone UI, each one built from the same fixed set of components (quick replies, rate cards, confirmation overlays, and more) rather than free-form text, so every response is structured the same way
- **Live editing**: edit any bubble, flip personas, apply tweaks and regenerate immediately
- **Prompt spec export**: export the full scenario set and guidelines as a shareable HTML or JSONL file, the same shape you'd use to seed a test set or evaluation run

---

## How to use

1. Download and install [Cowork](https://claude.ai/download) (free, requires a Claude account).
2. Open Cowork, drag and drop `turncraft-chatbot-conversation-designer.html` into the chat (example prompt: "run this tool as an artifact"). Opening the .html file directly in your web browser will not work.
3. Follow the onboarding: choose your domain, upload or use the built-in materials.
4. Enter a user intent and start designing.

The tool works out of the box with the bundled materials. You can replace them with your own conversation guidelines and design system at any time.

---

## Context & knowledge management

Turncraft doesn't hardcode a single voice or design system into the tool itself. Every response is generated against a knowledge base you control: a set of materials loaded in per session, and swappable at any time without touching the underlying tool.

Change a guideline or swap in a different design system, and every scenario in that session regenerates against the new materials immediately. The guidelines are context fed into the tool, which is what makes the same tool usable across different teams and domains. This works similarly in spirit to grounding a model in an external knowledge source, though materials here are swapped in as whole documents rather than retrieved piece by piece.

The tool ships with three materials as a working default, used to govern scenario generation and response design:

| File | Description | Source |
|------|-------------|--------|
| `unified-conversation-design-principles.md` | Core conversation design principles covering clarity, cooperation, turn structure, and tone | Adapted from H.P. Grice, *Logic and Conversation* (1975), and [Google Conversation Design](https://developers.google.com/assistant/conversation-design) (CC BY 4.0). Interpreted and extended for chatbot UI. |
| `design-system-material3.md` | Component usage guidance for chatbot UI based on Material Design 3 | Adapted from [Material Design 3](https://m3.material.io/) by Google (CC BY 4.0). Interpreted and extended for chatbot UI. |
| `design-system-ant-design-x.md` | Component guidance based on Ant Design X, an AI-native chat library | Adapted from [Ant Design X](https://x.ant.design/) by Ant Group (MIT). |

You can swap any of these out for your own materials from the Knowledge base panel inside the tool. Loading new materials is scoped to your current session, so switching context doesn't require redeploying or editing the tool itself.

---

## Evaluation & versioning

The bundled guidelines are tracked and tested like any other asset that governs model behavior. Changes are version-controlled with real commit history, and every version is checked against a fixed test set, a rubric written before generation, and independent cold-validation runs before being considered settled. One worked example is documented in full as a separate eval log: the [Rate Lock scenario cluster](https://cindysuryautama.github.io/turncraft-chatbot-conversation-designer/), tested across three guideline versions.

---

## License

This tool is released under the [MIT License](LICENSE).

The bundled materials are derivative works, see attribution above. Original sources are licensed under CC BY 4.0 (Google) and MIT (Ant Group). Attribution notices must be preserved when redistributing.

---

## Credits

Built by [Cindy Suryautama Sukiato](https://www.linkedin.com/in/cindy-suryautama-sukiato-17526553/).

Conversation design principles adapted from H.P. Grice (1975) and Google Conversation Design (CC BY 4.0).  
Design system materials adapted from Material Design 3 by Google (CC BY 4.0) and Ant Design X by Ant Group (MIT).
