# Turncraft: Chatbot conversation designer

An AI-powered tool for designing governed chatbot conversations. Enter a user intent → map the full scenario space → generate phone-sim responses with your design system and guidelines baked in.

Built as a Claude artifact. Runs in [Cowork](https://claude.ai/download), Anthropic's desktop app.
---

## What it does

- **Scenario mapping** — input a user intent and the tool expands the full scenario space: happy paths, error states, and edge cases
- **Governed response generation** — conversations are generated with your conversation guidelines and design system applied, not added as an afterthought
- **Phone simulator** — responses render in an interactive phone UI with real components (quick replies, rate cards, confirmation overlays, and more)
- **Live editing** — edit any bubble, flip personas, apply tweaks and regenerate immediately
- **Prompt spec export** — export the full scenario set and guidelines as a shareable HTML or JSONL file

---

## How to use

1. Download and install [Cowork](https://claude.ai/download) (free, requires a Claude account).
2. Open Cowork, drag and drop `turncraft-chatbot-conversation-designer.html` into the chat (example prompt: "run this tool as an artifact"). **Opening the .html file directly in your web browser will not work.**
3. Follow the onboarding — choose your domain, upload or use the built-in materials.
4. Enter a user intent and start designing.

The tool works out of the box with the bundled materials. You can replace them with your own conversation guidelines and design system at any time.

---

## Bundled materials

The tool ships with three training materials used to govern scenario generation and response design.

| File | Description | Source |
|------|-------------|--------|
| `unified-conversation-design-principles.md` | Core conversation design principles covering clarity, cooperation, turn structure, and tone | Adapted from H.P. Grice, *Logic and Conversation* (1975), and [Google Conversation Design](https://developers.google.com/assistant/conversation-design) (CC BY 4.0). Interpreted and extended for chatbot UI. |
| `design-system-material3.md` | Component usage guidance for chatbot UI based on Material Design 3 | Adapted from [Material Design 3](https://m3.material.io/) by Google (CC BY 4.0). Interpreted and extended for chatbot UI. |
| `design-system-ant-design-x.md` | Component guidance based on Ant Design X, an AI-native chat library | Adapted from [Ant Design X](https://x.ant.design/) by Ant Group (MIT). |

You can swap these out for your own materials from the Knowledge base panel inside the tool.

---

## License

This tool is released under the [MIT License](LICENSE).

The bundled materials are derivative works — see attribution above. Original sources are licensed under CC BY 4.0 (Google) and MIT (Ant Group). Attribution notices must be preserved when redistributing.

---

## Credits

Built by [Cindy Suryautama Sukiato](https://www.linkedin.com/in/cindy-suryautama-sukiato-17526553/).

Conversation design principles adapted from H.P. Grice (1975) and Google Conversation Design (CC BY 4.0).  
Design system materials adapted from Material Design 3 by Google (CC BY 4.0) and Ant Design X by Ant Group (MIT).
