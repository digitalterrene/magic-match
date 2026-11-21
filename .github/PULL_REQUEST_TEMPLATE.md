## 📌 Summary of Changes

A concise, high-level overview of what this pull request changes and why.

---

## 🎯 Problem Statement & Context

What bug does this fix, or what feature does this implement? Link relevant issues.
Fixes # (issue)

---

## 🛠️ Technical Approach & Implementation Details

Explain the architectural or implementation strategy. Note any state management, animation, or algorithm trade-offs:
- [ ] Point 1
- [ ] Point 2

---

## 🧪 Proof of Verification (Non-Negotiable)

Provide concrete evidence that this change works as intended and introduces no regressions:

### Automated Tests & Build:
```text
Paste output of `CI=true npm test` and `npm run build` here
```

### Visual Proof (For UI/Animation changes):
*Attach screenshots or a screen recording showing before and after states:*

| Before | After |
|---|---|
| *Screenshot* | *Screenshot* |

---

## ⚠️ Breaking Changes

- [ ] **No breaking changes** introduced.
- [ ] **Breaking change**: Describe backwards-incompatible changes and rollback steps below.

---

## ✅ Senior Pre-Flight Checklist

- [ ] My code adheres to the repository's [Git Standards](./docs/git-standards.md).
- [ ] Commits follow the [Conventional Commits](https://www.conventionalcommits.org/) specification.
- [ ] Non-trivial changes include Chain-of-Thought reasoning in commit bodies.
- [ ] Production build passes cleanly with zero ESLint errors (`npm run build`).
- [ ] Unit tests pass cleanly (`CI=true npm test`).
- [ ] Staged diff verified to contain **NO secrets**, credentials, or debug logs.
- [ ] Relevant documentation (`README.md`, `docs/architecture/`, `docs/adr/`) has been updated.
