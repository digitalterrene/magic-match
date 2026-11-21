# Git Engineering Non-Negotiables & Repository Hygiene

To maintain a production-grade codebase, all engineers and automated AI agents contributing to **Magic Match** must adhere strictly to the following standards. **Pull requests violating these rules will be rejected.**

---

## 1. Clean Branching Strategy

All work must occur on isolated branches cut directly from `main`. Direct commits to `main` should be avoided in favor of reviewed Pull Requests.

**Naming Conventions:**
- Features: `feat/<scope>-<kebab-case-description>`
- Bug Fixes: `fix/<scope>-<kebab-case-description>`
- Refactoring: `refactor/<scope>-<kebab-case-description>`
- Maintenance: `chore/<scope>-<kebab-case-description>`
- Documentation: `docs/<scope>-<kebab-case-description>`

*Examples*:
- `feat/game-fisher-yates-shuffle`
- `fix/card-prevent-double-click`
- `docs/architecture-add-state-diagrams`
- `chore/ci-add-gitleaks-scan`

---

## 2. Atomic Conventional Commits & Chain-of-Thought (CoT)

Every commit must be atomic (one logical unit of change) and follow the [Conventional Commits](https://www.conventionalcommits.org/) specification.

**Format**:
```text
<type>(<scope>): <short summary in imperative mood>

[Optional Body: Chain-of-Thought (CoT) explanation of Root Cause, Solution, and Verification Proof]

[Optional Footer: BREAKING CHANGE, Closes #issue]
```

**Allowed Types:**
- `feat`: A new user-facing feature (e.g., victory modal, difficulty modes).
- `fix`: A bug fix (e.g., preventing double-clicking the same card).
- `refactor`: Code restructuring without behavior changes.
- `perf`: Performance or rendering optimization.
- `test`: Adding or updating Jest / React Testing Library tests.
- `style`: CSS or formatting adjustments with no logic changes.
- `docs`: Documentation, diagrams, ADRs, or runbooks.
- `chore`: Build tooling, CI workflows, or dependency updates.

---

## 3. Pre-Commit Verification Gates

Zero tolerance for broken code on `main`:
- **Production Build**: `npm run build` exits with code `0` (includes CRA ESLint verification).
- **Automated Tests**: `CI=true npm test` executes cleanly with zero test failures.
- **Secret Hygiene**: `git diff --cached` contains zero credentials, tokens, private keys, or diagnostic dumps.

---

## 4. Credential & Secret Protection

- **NEVER** commit secrets, API keys, or private certificates to version control.
- Keep `.gitignore` hardened against sensitive files, runtime logs, `node_modules/`, and `build/`.
- Automated secret scanning (`gitleaks`) runs on every push and Pull Request via GitHub Actions.

---

## 5. Pull Request Completeness

Every Pull Request must include:
- **Problem Statement**: What bug or feature requirement is being addressed?
- **Technical Approach**: Summary of component, state, or tooling changes.
- **Proof of Verification**: Terminal output from tests/build and screenshots for UI changes.
- **Breaking Changes**: Explicit callout and migration/rollback steps if applicable.
