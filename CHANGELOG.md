# Changelog

All notable changes to Read It Aloud will be documented in this file across all browser versions.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.3.3] — 2026-06-12

### Changed
- **Chrome:** Support footer copy refreshed — now references all three BMaC tiers gracefully ("drop a coffee, become a monthly supporter, or settle the tab for life") via a single "Support the project" button.
- **Chrome:** BMaC slug cased to `ReadItAloud` to match the canonical URL.

---

## [1.3.2] — 2026-06-11

### Added
- **Chrome:** Support footer in popup with "Buy Me a Coffee" and "GitHub Sponsors" buttons.
- **Chrome:** README repositioned around ADHD / dyslexia / accessibility audience.
- **Chrome:** Store listing copy added with audience-led short and long descriptions.

---

## [1.3.1] — 2026-06-11

### Changed
- **Chrome:** Dual-tone outline on the floating play button and mini-player so the widget is visible on any page background (light or dark).

---

## [1.3.0] — 2026-06-11

### Added
- **Chrome:** Scrollable follow-along text panel — current sentence highlights and the panel scrolls with the audio.
- **Chrome:** Friendly API error messages for 401, 402, 403, 404, 422, 429, and 5xx responses.
- **Chrome:** Multi-chunk concurrency cap at 3 simultaneous in-flight requests.
- **Chrome:** Long-selection (>25K chars) warning shown to the user.
- **Chrome:** Full ARIA coverage on all controls; aria-live regions; aria-valuenow on progress bar.

### Added (project-level)
- **Privacy policy** ([PRIVACY.md](./PRIVACY.md))
- **Chrome Web Store listing copy** drafted

---

## [1.2.0] — 2026-06-11

### Fixed
- **Chrome:** Stop button now cancels in-flight ElevenLabs requests via `AbortController`. Previously, stopping playback did not stop the API request — burning ElevenLabs credits on audio that would never play.

### Removed
- **Chrome:** Dead iframe-based player code (`player-frame.html`, `player-frame.js`).
- **Chrome:** `web_accessible_resources` entry removed since the iframe approach is no longer used.

---

## [1.1.0] — 2026-06-11

### Fixed
- **Chrome:** Audio playback on strict-CSP sites (claude.ai, etc.) that block blob URLs and inline media. The extension now routes audio playback through a `chrome.offscreen` document, which is exempt from page-level CSP, autoplay policy, and media-safety checks.

---

## Browser launch milestones

- **Chrome / Brave / Edge / Opera** — initial release (v1.x series)
- **Firefox** — in development, target v2.0
- **Safari (macOS + iOS)** — in development, target v2.0
