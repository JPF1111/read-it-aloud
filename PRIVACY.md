# Privacy Policy — Read It Aloud

**Last updated:** June 12, 2026
**Effective:** When you install or update to v1.3.0 or later

## Summary in plain English

- We don't run a server. There is no Read It Aloud backend.
- The extension takes text **you select** on a web page and sends it to
  **ElevenLabs** so they can generate audio for you. That's the only data
  that leaves your browser.
- Your ElevenLabs **API key** is stored locally in your browser
  (`browser.storage.local` / `chrome.storage.local`) and is sent **only**
  to ElevenLabs' servers (`https://api.elevenlabs.io`).
- We do **not** collect analytics, telemetry, crash reports, or any
  identifier about you. We do not have a database. We do not sell or
  share anything because we don't have anything.

## What data the extension handles

| Data | Where it lives | Where it's sent | Why |
|------|----------------|------------------|-----|
| Your ElevenLabs API key | `storage.local` on your device only | `api.elevenlabs.io` as the `xi-api-key` HTTP header | Required to authenticate TTS requests |
| Selected text on a web page | RAM (transient) and the extension's offscreen audio document | `api.elevenlabs.io` in the TTS request body | Required to generate the audio you asked for |
| Your voice & playback settings (voice ID, model, speed, stability, etc.) | `storage.local` on your device only | `api.elevenlabs.io` as part of TTS request body | Required so generation matches your preferences |
| Generated audio bytes | RAM and the offscreen audio document; discarded when you stop or close the tab | Stays on your device | Played back to you, optionally downloadable |

Nothing is sent to any other party. The extension makes no network calls
to any host except `api.elevenlabs.io`.

## What we do NOT collect

- No analytics or telemetry
- No browsing history, page content, or URLs (beyond what you select)
- No personally identifiable information
- No advertising identifiers
- No cookies, fingerprints, or device IDs
- No data is sent to the extension author

## Third-party services

The selected text and your voice settings are sent to **ElevenLabs**,
the TTS provider. ElevenLabs is governed by their own privacy policy:
<https://elevenlabs.io/privacy>. You hold an account directly with
ElevenLabs (this extension uses *your* API key) and your relationship
with them is between you and ElevenLabs.

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

## Changes to this policy

We'll update this document if the data practices change. The
"Last updated" date at the top reflects the current version.
Material changes will be noted in [CHANGELOG.md](./CHANGELOG.md).

## Contact

For questions about this policy, open an issue at
<https://github.com/JPF1111/read-it-aloud/issues>.
