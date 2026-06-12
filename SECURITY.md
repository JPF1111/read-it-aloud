# Security Policy

## Reporting a vulnerability

If you believe you've found a security vulnerability in Read It Aloud,
please **do not file a public issue.** Reports of real vulnerabilities
in a browser extension that handles API keys deserve a private channel.

### How to report

Open a [private security advisory](https://github.com/JPF1111/read-it-aloud/security/advisories/new)
on this repository. GitHub will route it directly to the maintainer
without exposing the report publicly.

Please include:

- A clear description of the vulnerability
- The browser and extension version affected
- Steps to reproduce
- The impact you believe it has
- Any suggested mitigation, if you have one

### What's in scope

- The extension code itself across any browser (Chrome / Edge / Brave / Opera / Firefox / Safari)
- The build / packaging process for any published version
- The handling of the user's ElevenLabs API key
- Any code that sends data to ElevenLabs or receives data back

### What's out of scope

- The ElevenLabs API itself — report those to <https://elevenlabs.io/security>
- The browser's own permission model
- Third-party browsers' extension stores
- Social engineering attacks against extension users
- Issues that require a malicious extension to already be installed alongside this one

### What to expect

- Acknowledgement of the report within a few days
- A reasonable effort to triage, validate, and respond
- Credit in the release notes for the fix (unless you prefer anonymity)

Read It Aloud is a one-person side project. There is no SLA, no
bounty program, and no formal disclosure window. Reports are
appreciated and handled in good faith.

---

## Supported versions

Only the latest released version of each browser is actively supported.
Older versions should be uninstalled and the latest installed from the
relevant browser's store.

See [CHANGELOG.md](./CHANGELOG.md) for the current version per browser.
