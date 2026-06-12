# Read It Aloud

> **Listen to the web.** Built for ADHD, dyslexia, and tired eyes.

[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-FFDD00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black)](https://buymeacoffee.com/ReadItAloud)
[![GitHub Sponsors](https://img.shields.io/badge/Sponsor-30363D?style=for-the-badge&logo=GitHub-Sponsors&logoColor=#EA4AAA)](https://github.com/sponsors/JPF1111)
[![License: PolyForm Noncommercial](https://img.shields.io/badge/License-PolyForm%20Noncommercial-blue?style=for-the-badge)](./LICENSE)

A browser extension that takes any text you select on a webpage and reads it aloud in a natural, human-sounding voice — powered by your own ElevenLabs account.

---

## Why this exists

I have ADHD. Reading dense articles, court filings, research papers, and long PDFs on a screen was a constant uphill battle — the words would blur, my focus would drift, and 20 minutes would vanish for 3 paragraphs of progress.

Audio changed that. Listening — especially at 1.5x or 2x — engages a completely different part of focus than visual reading. It cut through the wall.

I built **Read It Aloud** for myself first. Then it became clear how many other people it could help:

- **ADHD** — audio sustains attention where text loses it
- **Dyslexia** — hearing words removes the decoding tax of reading
- **Visual fatigue, eye strain, post-concussion sensitivity**
- **Language learners** — natural pronunciation of any text on any page
- **Anyone who wants to "read" while cooking, walking, driving, or resting their eyes**
- **Accessibility users** who want higher-quality voices than built-in screen readers for casual reading

The extension is **free and always will be**. No ads. No analytics. No tracking. No data collection of any kind. Your ElevenLabs API key stays on your device.

---

## Features

- **Floating play button** on any text you select
- **Right-click menu** integration
- **Full playback controls** — play, pause, stop, rewind 10s, forward 10s, scrubbing
- **Speed control** — 0.5x to 2.0x with smooth playback
- **Follow-along text panel** — scrolls with the audio so you never lose your place
- **Voice library** — your cloned/custom voices plus ElevenLabs' built-in library
- **Multiple models** — Eleven v3, Turbo v2.5, and others
- **MP3 download** — save the generated audio
- **Light and dark themes** that match your system
- **Works on strict-CSP sites** — Gmail, Google Docs, court portals, AI chat platforms, news paywalls
- **Fully accessible** — every control has an `aria-label`, screen-reader friendly

---

## Browser support

Read It Aloud is being built for every major browser. Status as of v1.3.3:

| Browser | Status | Install |
|---------|--------|---------|
| **Chrome** | ✅ Submitting to Chrome Web Store | [Install guide](./docs/install-chrome.md) |
| **Edge** | 🟡 Compatible — listing in progress | [Install guide](./docs/install-edge.md) |
| **Brave** | ✅ Works via the Chrome Web Store version | [Install guide](./docs/install-chrome.md) |
| **Opera** | 🟡 Compatible — listing in progress | [Install guide](./docs/install-edge.md) |
| **Firefox** | 🛠️ In development | [Install guide](./docs/install-firefox.md) |
| **Safari** | 🛠️ In development | [Install guide](./docs/install-safari.md) |

Each browser version is tracked in [CHANGELOG.md](./CHANGELOG.md).

---

## Privacy

We don't run a server. We don't collect anything. We don't have anything to sell.

The full privacy policy lives at [PRIVACY.md](./PRIVACY.md). The short version:

- Your selected text is sent **only** to ElevenLabs (so they can generate the audio you asked for)
- Your ElevenLabs API key lives **only** on your device
- No analytics, no telemetry, no identifiers, no third parties

---

## Support the project

Read It Aloud is free and built by one person on the side. If it has helped you focus, learn, work, or just rest your eyes, support keeps development going.

- [☕ Buy Me a Coffee](https://buymeacoffee.com/ReadItAloud) — one-time tip, monthly Supporter ($3/mo), or Kindred lifetime support ($33)
- [💚 GitHub Sponsors](https://github.com/sponsors/JPF1111) — recurring support through GitHub

Every contribution funds development time, ElevenLabs credits for testing new voices and features, and the next round of accessibility improvements.

---

## License

This project is licensed under the **PolyForm Noncommercial License 1.0.0**. See [LICENSE](./LICENSE) for the full text.

In plain English:

- **Free for personal use, individuals, students, educators, charities, non-profits, accessibility organizations, and any noncommercial purpose**
- **Commercial use is not permitted** without a separate commercial license from the author
- You may use, modify, and share the project freely for noncommercial purposes
- Commercial licensing inquiries are welcome — contact the author through this repository

This license exists because Read It Aloud was built to help individuals and communities who need accessibility tools, not to be repackaged and resold by companies.

---

## Bug reports & feature requests

Issues and feature requests are welcome at [the issue tracker](https://github.com/JPF1111/read-it-aloud/issues).

For security reports, see [SECURITY.md](./SECURITY.md).

For contributions, see [CONTRIBUTING.md](./CONTRIBUTING.md).
