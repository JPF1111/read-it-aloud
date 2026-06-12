# Install Read It Aloud on Firefox

> 🛠️ **Status:** In development.
>
> The Firefox version is being built. Mozilla supports Manifest V3
> extensions and the underlying code shares most of its surface with the
> Chrome version, but Firefox has its own extension APIs, packaging
> tooling, and review process at [addons.mozilla.org](https://addons.mozilla.org/).
>
> Target release: **v2.0**
>
> Star the [GitHub repository](https://github.com/JPF1111/read-it-aloud) to
> be notified when the Firefox version is available.

---

## What to expect

The Firefox release will be functionally identical to the Chrome version:

- Floating play button on text selection
- Right-click menu integration
- Full playback controls (play, pause, stop, rewind, forward, speed)
- Follow-along text panel
- ElevenLabs voice library
- MP3 download
- Same privacy posture — no data collection, API key stored locally

---

## Why Firefox is taking a separate release

Firefox uses some browser APIs differently from Chrome (`browser.*`
namespace, separate offscreen audio handling, different packaging
constraints), and Mozilla's review process operates on its own timeline.
Releasing a polished Firefox version takes more than just renaming the
zip. We'd rather ship it right than ship it fast.

---

## Once it's live

Install will be a one-click flow from
[addons.mozilla.org](https://addons.mozilla.org/) — same as any other
Firefox add-on. This page will be updated with the direct install link.

---

## Privacy

Same policy across all browser versions: [PRIVACY.md](../PRIVACY.md).
