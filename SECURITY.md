# Security Policy

## Supported Versions

| Version | Supported |
| --- | --- |
| Latest release | ✅ |
| Older releases | ❌ |

Only the latest published version receives security fixes. We recommend always running the most recent release.

## Reporting a Vulnerability

**Please do not report security vulnerabilities through public GitHub issues.**

If you discover a security vulnerability in Clinear, please report it responsibly by emailing **both**:

- **Fabian Jocks** — [dev@iamfj.xyz](mailto:dev@iamfj.xyz)
- **Carlo Zottmann** — [carlo@zottmann.dev](mailto:carlo@zottmann.dev)

Please include:

- A description of the vulnerability
- Steps to reproduce the issue
- The potential impact
- Any suggested fixes (optional, but appreciated)

## What to Expect

- **Acknowledgment** within 48 hours of your report.
- **Status update** within 7 days with an assessment and expected timeline.
- **Fix and disclosure** — we aim to release a fix within 14 days of confirmation. You will be credited in the release notes unless you prefer otherwise.

## Scope

The following are in scope for security reports:

- Authentication token handling (storage, transmission, leakage)
- Arbitrary file read/write via CLI input
- Command injection or path traversal
- Dependency vulnerabilities that are exploitable in Clinear

The following are **out of scope**:

- The hardcoded encryption key in token storage (this is documented as obfuscation-level protection, not cryptographic security)
- Vulnerabilities in Linear's API itself (report those to [Linear](https://linear.app))

## Encryption Disclaimer

Clinear stores API tokens with obfuscation-level encryption using a key embedded in the source code. This protects against casual exposure (e.g., accidental file sharing, git commits) but **does not protect** against a determined attacker with access to the binary or source. This is a known design trade-off, not a vulnerability.

## Thank You

We appreciate the security research community and anyone who takes the time to report issues responsibly. Thank you for helping keep Clinear and its users safe.
