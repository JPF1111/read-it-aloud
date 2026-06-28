# Changelog

All notable changes to Read It Aloud will be documented in this file across all browser versions.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.3.5] — 2026-06-27

### Added
- **Anonymous uninstall feedback.** When the extension is removed, the browser opens a short, optional survey asking why. If you submit it, an anonymous reason, an optional comment, and the version number are recorded to help prioritize fixes and features — no personal data, no identifiers. Closing the page sends nothing.

### Changed
- **Privacy policy updated** to disclose the optional, anonymous uninstall feedback endpoint and exactly what it records.

---

## [1.3.4] — 2026-06-12

### Fixed
- **Chrome:** **Stop button no longer locks the player.** Previously, pressing Stop discarded the playback session, leaving play, skip, and seek unresponsive until the player was closed and the text re-selected. Stop now halts playback and rewinds to the start while keeping the generated audio — press play to replay, or scrub and skip freely. (In-flight ElevenLabs requests are still aborted on Stop to protect your quota.)
- **Chrome:** **Dark-mode contrast fixes.** The play/pause icon was nearly invisible against the control button in dark mode — now renders clearly. Field borders, the text panel edge, and the progress track were too faint against the dark background and are now visible.

---

## [1.3.3] — 2026-06-12

### Changed
- **Chrome:** Support footer copy refreshed — now references all three BMaC tiers gracefully ("drop a coffee, become a monthly supporter, or settle the tab for life") via a single "Support the project" button.
- **Chrome:** BMaC slug cased to `ReadItAloud` to match the canonical URL.
- **Chrome:** Manifest description rewritten to lead with the accessibility audience — "Text to speech for any webpage. Built for ADHD, dyslexia & tired eyes. Natural ElevenLabs voices with full playback controls." This is the text the Chrome Web Store displays as the listing summary.

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
