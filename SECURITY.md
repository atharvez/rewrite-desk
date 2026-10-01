# Security Policy

## Scope

Rewrite Desk is a **client-side only** application. It runs entirely in your browser with no server, no accounts, and no network requests at runtime (aside from optional CDN fallback for vendor dependencies on first load).

- **State storage**: All document state is saved to `localStorage` in your browser. No data is transmitted anywhere.
- **No PII stored**: The application stores document text, style-guide rules, and UI preferences only. It does not collect, transmit, or persist any personally identifiable information.
- **No backend**: There is no server, database, API, or authentication layer to attack.

## Reporting a Vulnerability

If you discover a security vulnerability in Rewrite Desk, **please do not open a public GitHub issue**. Public disclosure before a fix is available puts other users at risk.

Instead, send a report by email to:

**security@rewritedesk.dev**

Please include:

- A clear description of the vulnerability and the potential impact
- Steps to reproduce the issue (browser version, OS, and any relevant configuration)
- Any proof-of-concept code or screenshots, if applicable
- Your preferred contact method for follow-up questions

### Response Timeline

| Milestone | Target |
|---|---|
| Acknowledgement of your report | Within **72 hours** |
| Initial assessment and triage | Within **7 days** |
| Fix or mitigation released | Dependent on severity; critical issues are prioritised |
| Public disclosure (coordinated) | After a fix is available and users have had reasonable time to update |

We will keep you informed throughout the process and will credit you in the release notes unless you prefer to remain anonymous.

## Out of Scope

The following are **not considered in-scope** vulnerabilities for this project:

- **Issues requiring a backend**: Rewrite Desk has no backend. Reported issues that presuppose a server component are not applicable.
- **Browser vulnerabilities**: Bugs in the browser itself (Chrome, Firefox, Safari, etc.) should be reported to the respective browser vendor.
- **CDN supply-chain attacks**: Rewrite Desk vendors its dependencies locally (`vendor/diff.js`, `vendor/marked.min.js`) and uses CDN only as a fallback when vendor files are absent. Attacks on third-party CDN infrastructure are outside our control and should be reported to the CDN provider.
- **Self-XSS**: Attacks that require the victim to run malicious code in their own browser console.
- **Physical access attacks**: Scenarios requiring physical access to an unlocked device.
- **Denial of service via localStorage quota**: Filling a user's localStorage quota is a browser-level concern, not an application vulnerability.

## Supported Versions

Only the latest released version of Rewrite Desk receives security fixes. If you are running an older version, please update to the latest release before submitting a report.

| Version | Supported |
|---|---|
| Latest release | ✅ Yes |
| Older releases | ❌ No |
