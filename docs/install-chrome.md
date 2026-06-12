# Install Read It Aloud on Chrome (and Chromium browsers)

This guide covers Chrome and other Chromium-based browsers that accept
extensions from the Chrome Web Store: **Brave, Vivaldi, Arc, and most
Chromium forks**.

For Microsoft Edge specifically, see the [Edge install guide](./install-edge.md)
since Edge has its own dedicated add-ons store (though the Chrome Web Store
version also works on Edge).

---

## From the Chrome Web Store *(recommended)*

> 🔜 **Status:** Submission in progress. This guide will be updated with the
> direct install link as soon as the listing is published. Star the
> [GitHub repository](https://github.com/JPF1111/read-it-aloud) to be
> notified when it goes live.

When the listing is live, the install flow will be:

1. Open the Chrome Web Store page (link will be added here)
2. Click **Add to Chrome**
3. Confirm the permissions dialog
4. Click the extension icon in your toolbar to set up your ElevenLabs API key

---

## Setup after install

1. Click the Read It Aloud icon in your browser toolbar
2. Paste your ElevenLabs API key
   - Don't have one? [Get a free key from ElevenLabs](https://try.elevenlabs.io/nnk6n3ye9kev)
3. Choose a voice
4. Adjust settings (speed, stability, similarity) if you want

When creating your ElevenLabs API key, make sure to enable:

- **Text to Speech** — Access
- **Voices** — Read
- **User** — Read

---

## Usage

### Read selected text on any page

1. Highlight any text on a webpage
2. Click the floating "Read It Aloud" button that appears next to your
   selection, **or** right-click and choose "Read It Aloud" from the
   context menu
3. The inline mini-player will appear with full playback controls

### Convert and download arbitrary text as MP3

1. Click the extension icon
2. Switch to the "Text to Audio" tab
3. Paste your text and click "Convert and Download"

---

## Troubleshooting

### "Failed to load because no supported source was found"

Some sites (claude.ai, certain news paywalls, court portals) have strict
Content Security Policies that block audio playback in page context.
Read It Aloud handles this automatically by playing audio in an
extension-owned offscreen document. If you still hit this error,
please [open an issue](https://github.com/JPF1111/read-it-aloud/issues)
with the site URL.

### Audio cuts off mid-sentence

Long selections are chunked into multiple audio requests. The chunks
should join seamlessly — if they don't, please open an issue with the
text you tried to read.

### The floating button doesn't appear

Check that "Show floating play button on text selection" is enabled in
the extension settings (gear icon in the popup).

### Voices aren't loading

Verify your ElevenLabs API key is correct and that it has the required
permissions listed in the setup section. The popup will show a clear
error message if the key is rejected.

---

## Privacy

The extension does not collect or transmit any data about you. Your
selected text is sent only to ElevenLabs for audio generation, using
your own API key. Full policy: [PRIVACY.md](../PRIVACY.md).

---

## Uninstall

Right-click the extension icon → **Remove from Chrome**. This deletes
your locally stored API key and all settings.
