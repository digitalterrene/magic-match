# Security Policy & Vulnerability Reporting

The security of **Magic Match**, its build pipeline, and its users is a top priority. We appreciate responsible disclosure of security issues and commit to investigating and resolving valid reports promptly.

---

## 1. Supported Versions

We actively maintain and provide security updates for the following branches/versions:

| Version | Supported | Status |
|---|---|---|
| `main` (`0.1.x`) | :white_check_mark: | Actively maintained |
| Older commits / forks | :x: | Unsupported — please update to `main` |

---

## 2. Scope & Threat Model

Magic Match is a **client-side React Single-Page Application (SPA)**. Relevant security concerns include:
- **Supply Chain & Dependencies**: Known CVEs in `npm` dependencies (`react`, `react-scripts`, webpack/dev-server tooling).
- **Client-Side Security**: Cross-Site Scripting (XSS), unsafe DOM injection, or insecure static hosting configurations (missing CSP / clickjacking headers).
- **Repository Hygiene**: Accidental credential exposure in git history or CI workflow misconfigurations (e.g., GitHub Actions script injection).

See [`docs/architecture/security.md`](./docs/architecture/security.md) for the full threat model and recommended HTTP security headers.

---

## 3. Reporting a Vulnerability

**DO NOT file public GitHub issues for security vulnerabilities.**

Please report vulnerabilities privately via **GitHub Security Advisories** on [`digitalterrene/magic-match`](https://github.com/digitalterrene/magic-match/security/advisories) or contact the repository maintainers directly.

Include the following details in your report:
- **Component / File**: Affected module, dependency, or CI workflow.
- **Vulnerability Type**: e.g., Dependency CVE, XSS, CI Token Exposure, Supply Chain Risk.
- **Proof of Concept**: Minimal reproduction steps or advisory link.
- **Impact Assessment**: Potential blast radius in development or production static deployments.

---

## 4. Vulnerability Response SLA

| Severity | Initial Response | Assessment & Triage | Resolution Target |
|---|---|---|---|
| **Critical** | Within 12 hours | Within 24 hours | 48–72 hours |
| **High** | Within 24 hours | Within 48 hours | 7 business days |
| **Medium / Low** | Within 72 hours | Within 5 business days | Next release cycle |

---

## 5. Responsible Disclosure Guidelines

- Do not exploit vulnerabilities beyond minimal proof-of-concept verification.
- Allow maintainers reasonable time to patch and release a fix before public disclosure.
- Contributors who responsibly report valid issues will be credited in release notes upon request.
