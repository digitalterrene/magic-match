# Contributing to Magic Match

Thank you for your interest in contributing to **Magic Match**. Maintaining a clean, predictable codebase requires clear standards so every engineer and automated agent can ship safely and confidently.

---

## 1. Code of Conduct

We are committed to providing a friendly, safe, and welcoming environment for all contributors regardless of experience level, background, or identity. Please treat everyone with respect and professional empathy.

---

## 2. Engineering Non-Negotiables

Before opening a Pull Request, you **must** read and follow our [Git Engineering Standards](./docs/git-standards.md).

**Key Rules:**
1. **Branch Naming**: Branch from `main` using `feat/<scope>-<desc>`, `fix/<scope>-<desc>`, `refactor/<scope>-<desc>`, `docs/<scope>-<desc>`, or `chore/<scope>-<desc>`.
2. **Conventional Commits**: Every commit must adhere to the [Conventional Commits](https://www.conventionalcommits.org/) specification.
3. **Chain-of-Thought (CoT)**: For non-trivial logic changes, bug fixes, or agent-assisted work, document the root cause, rationale, and verification proof in the commit body.
4. **Pre-Commit Verification**: Your PR must build cleanly (`npm run build`), pass tests (`CI=true npm test`), and introduce zero ESLint errors or leaked secrets.

---

## 3. Local Development Workflow

1. Fork or clone the repository:
   ```bash
   git clone https://github.com/digitalterrene/magic-match.git
   cd magic-match
   ```
2. Create an isolated feature branch from `main`:
   ```bash
   git checkout -b feat/game-fisher-yates-shuffle
   ```
3. Install dependencies and start the dev server:
   ```bash
   npm install
   npm start
   ```
4. Make atomic, well-tested changes.
5. Verify quality gates locally before pushing:
   ```bash
   # Run automated tests in CI (non-watch) mode
   CI=true npm test

   # Verify production bundle compiles cleanly (runs CRA ESLint checks)
   npm run build
   ```

---

## 4. Architectural Modifications & ADRs

If your contribution changes foundational architecture (for example: introducing a state management library, migrating from Create React App to Vite, or changing the asset/animation architecture):
1. Review the [Architecture Blueprints](./docs/architecture/).
2. Submit an **Architecture Decision Record (ADR)** in [`docs/adr/`](./docs/adr/) using [`ADR-000-template.md`](./docs/adr/ADR-000-template.md) and update [`docs/adr/README.md`](./docs/adr/README.md).

---

## 5. Submitting a Pull Request

1. Rebase your branch against the latest `main`:
   ```bash
   git checkout main
   git pull origin main
   git checkout your-branch
   git rebase main
   ```
2. Push your branch and open a PR using our [Pull Request Template](./.github/PULL_REQUEST_TEMPLATE.md).
3. Include **Proof of Verification** (test output and/or UI screenshots for visual changes).
4. Ensure all GitHub Actions CI checks pass before requesting review.
