# Privacy Policy — Read It Aloud

**Last updated:** June 27, 2026
**Effective:** When you install or update to v1.3.0 or later

## Summary in plain English

- The extension has **no backend for its core features**. The text you select
  is sent to **ElevenLabs** to generate audio — that's the only data that leaves
  your browser while you use it.
- Your ElevenLabs **API key** is stored locally in your browser
  (`browser.storage.local` / `chrome.storage.local`) and is sent **only** to
  ElevenLabs' servers (`https://api.elevenlabs.io`).
- We don't run analytics, telemetry, or crash reporting, and we hold no
  identifiers about you.
- The **one** time anything reaches us is if you **choose** to submit the
  optional, anonymous **uninstall survey** — see "Uninstall feedback" below.
  It contains no personal information.
- We never sell or share your data.

## What data the extension handles

| Data | Where it lives | Where it's sent | Why |
|------|----------------|------------------|-----|
| Your ElevenLabs API key | `storage.local` on your device only | `api.elevenlabs.io` as the `xi-api-key` HTTP header | Required to authenticate TTS requests |
| Selected text on a web page | RAM (transient) and the extension's offscreen audio document | `api.elevenlabs.io` in the TTS request body | Required to generate the audio you asked for |
| Your voice & playback settings (voice ID, model, speed, stability, etc.) | `storage.local` on your device only | `api.elevenlabs.io` as part of TTS request body | Required so generation matches your preferences |
| Generated audio bytes | RAM and the offscreen audio document; discarded when you stop or close the tab | Stays on your device | Played back to you, optionally downloadable |

While the extension is running, it makes no network calls to any host except
`api.elevenlabs.io`.

## Uninstall feedback (optional & anonymous)

When you remove the extension, your browser opens a short feedback page asking
why you uninstalled. This is **completely optional** — closing the page sends
nothing.

If you **choose** to submit it, the following is sent and recorded as anonymous
feedback to help us improve the extension:

- the **reason** you select (from a fixed list),
- an **optional free-text comment**, if you write one,
- the **extension version** you were on.

That's all. The submission is **anonymous** — we do not ask for or store your
name, email, IP address, API key, browsing data, or any other identifier, and
there is no way for us to tie a response back to you. The feedback page is a
separate web page opened by your browser (not the extension), passed through a
small write-only endpoint (a Cloudflare Worker) and recorded as an anonymous
note on GitHub. It is used only to prioritize fixes and features.

## What we do NOT collect

- No analytics or telemetry
- No browsing history, page content, or URLs (beyond what you select to read)
- No personally identifiable information
- No advertising identifiers
- No cookies, fingerprints, or device IDs
- No identifying data is ever sent to the extension author — the only thing we
  can receive is the anonymous, optional uninstall feedback described above

## Third-party services

- **ElevenLabs** — the TTS provider. Your selected text and voice settings are
  sent here to generate audio. ElevenLabs is governed by its own privacy policy:
  <https://elevenlabs.io/privacy>. You hold an account directly with ElevenLabs
  (this extension uses *your* API key); that relationship is between you and them.
- **Cloudflare & GitHub** — used only to carry and store the anonymous uninstall
  feedback described above, and only if you choose to submit it. They never
  receive your reading content, API key, or any identifier.

## Permissions and what they're for

- **storage** — to save your API key and preferences locally on your device
- **activeTab** / **scripting** — to read the text you select on the active tab when you ask the extension to read it
- **contextMenus** — to add the "Read It Aloud" right-click menu item
- **offscreen** *(Chromium browsers)* — to play audio in an extension-owned offscreen document (technical requirement on strict-CSP sites; never accesses page content)
- **host_permissions: https://api.elevenlabs.io/\*** — to call the ElevenLabs TTS API. The extension cannot make requests to any other domain.

The exact set of permissions may vary slightly by browser due to platform differences (Chrome / Edge / Firefox / Safari implement extension APIs slightly differently). The underlying data practices above are identical across all versions.

## Your rights

- **Stop using the extension at any time.** Removing it from your browser
  deletes the locally stored API key and settings.
- **Reset your data** at any time from the extension settings.
- **Revoke your ElevenLabs API key** in your ElevenLabs dashboard at
  any time. The extension will stop working immediately.
- **Skip the uninstall survey** simply by closing the page — nothing is sent.

## Changes to this policy

We'll update this document if the data practices change. The
"Last updated" date at the top reflects the current version.
Material changes will be noted in [CHANGELOG.md](./CHANGELOG.md).

## Contact

For questions about this policy, open an issue at
<https://github.com/JPF1111/read-it-aloud/issues>.
