# Install Read It Aloud on Safari (macOS & iOS)

> 🛠️ **Status:** In development.
>
> The Safari version is being built. Safari Web Extensions use the same
> JavaScript / HTML / CSS code as Chrome and Firefox, but distribution
> goes through the **Mac App Store** and **iOS App Store** — meaning a
> Safari version of Read It Aloud will be available on both macOS *and*
> iPhone / iPad as a single multi-platform release.
>
> Target release: **v2.0**
>
> Star the [GitHub repository](https://github.com/JPF1111/read-it-aloud) to
> be notified when the Safari version is available.

---

## What's different about Safari

- **App Store distribution.** Safari Web Extensions are wrapped in a native
  Swift app and submitted through Apple's standard App Store review process.
- **Cross-device support.** A single Safari Web Extension can be packaged
  for both macOS and iOS. The same extension that runs in desktop Safari
  also runs on iPhone and iPad Safari with appropriate UI adaptations.
- **Tighter integration.** Safari Web Extensions integrate with Apple's
  privacy model (Intelligent Tracking Prevention, App Sandbox), which is
  actually well-aligned with Read It Aloud's "no data collection" stance.

---

## What to expect

The Safari release will offer the same core feature set:

- Floating play button on text selection
- Share-sheet integration on iOS for sending selected text to the extension
- Full playback controls
- ElevenLabs voice library
- Same privacy posture — no data collection, API key stored locally on
  device via Apple's secure storage

Some features may behave differently due to iOS constraints (background
audio handling, share-sheet UX rather than right-click menus, etc.).

---

## Why Safari is taking a separate release

Safari Web Extensions require:

- A Mac running the latest Xcode to build
- An Apple Developer Program membership ($99/year) to distribute
- Apple's App Store review process (typically 1–2 weeks per submission)
- A Swift wrapper app + the web extension code together

None of this is hard — it's a different release pipeline with its own
timeline. Releasing a polished Safari version takes more than just
recompiling the Chrome extension. We'd rather ship it right than ship
it fast.

---

## Once it's live

Install will be:

- **macOS:** App Store listing — install the Read It Aloud companion app,
  then enable the extension in Safari → Settings → Extensions.
- **iOS / iPadOS:** App Store listing — install the Read It Aloud app,
  then enable the extension in Settings → Safari → Extensions.

This page will be updated with the direct App Store links.

---

## Privacy

Same policy across all browser versions: [PRIVACY.md](../PRIVACY.md).

Safari and iOS additionally apply Apple's privacy framework on top of
the extension's own data practices — meaning Read It Aloud is subject
to App Tracking Transparency, App Sandbox, and the standard App Store
privacy nutrition labels (which will, per our policy, declare "no data
collected").
