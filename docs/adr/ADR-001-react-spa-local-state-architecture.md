# ADR-001: React 18 Client-Side SPA with Local Hook State

- **Status**: Accepted
- **Date**: 2026-11-21
- **Authors**: Core Engineering Team
- **Deciders**: Repository Maintainers
- **Technical Story**: Initial application architecture (`src/App.js`, `src/index.js`)

---

## 1. Context & Problem Statement

Magic Match is an interactive single-player memory card game consisting of a 12-card grid, turn counter, pair matching logic, and a reset button. We needed a frontend architecture that provides immediate UI responsiveness, minimal bundle complexity, and zero operational backend cost.

---

## 2. Decision

We will build Magic Match as a **zero-backend React 18 Single-Page Application (SPA)** bootstrapped with Create React App (`react-scripts` 5.0.1), managing all game state locally inside `src/App.js` via `useState` and `useEffect` hooks.

Implementation Details:
- `App.js` acts as the single stateful container owning `cards`, `turns`, `choiceOne`, `choiceTwo`, and `disabled`.
- `SingleCard.js` remains a pure presentational component receiving derived state (`flipped`) and callbacks (`handleChoice`) via props.
- Pair comparison and mismatch delay (`1000ms`) are orchestrated declaratively inside `useEffect([choiceOne, choiceTwo])`.

---

## 3. Consequences

### Positive Impacts
- **Zero Operational Overhead**: Compiles to static HTML/CSS/JS deployable to any CDN or static host.
- **Minimal Cognitive Load**: New contributors can read and understand the entire game engine in under 5 minutes (~95 lines in `App.js`).
- **Zero Extra State Dependencies**: No Redux, Zustand, or React Context boilerplate required for a 2-level component tree.

### Negative Impacts & Operational Costs
- **No Cross-Session Persistence**: Refreshing the browser resets the board and turn counter.
- **CRA Maintenance Lifecycle**: Create React App (`react-scripts` 5.0.1) is in maintenance mode; future upgrades may require migrating to Vite (to be captured in a future ADR).

### Risks and Mitigations
- **Risk**: Adding deep component nesting (modals, settings drawers, scoreboards) could introduce prop drilling.
- **Mitigation**: Re-evaluate React Context or a lightweight store (e.g., Zustand) via a new ADR if component depth exceeds 3 levels.

---

## 4. Alternatives Considered & Disqualification Rationale

### Option A: Redux Toolkit / Global Store
- *Pros*: Standardized DevTools time-travel debugging and predictable reducers.
- *Cons*: Over-engineered for 5 scalar/array state hooks in a single container component.

### Option B: Server-Rendered Multi-Page App / Backend API
- *Pros*: Enables persistent leaderboards and server-authoritative game state.
- *Cons*: Introduces network latency on card flips, hosting costs, and database maintenance unnecessary for a single-player memory game.
