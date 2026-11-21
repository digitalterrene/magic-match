# ADR-003: Static Public Asset Serving & In-Memory Deck Shuffling

- **Status**: Accepted
- **Date**: 2026-11-21
- **Authors**: Core Engineering Team
- **Deciders**: Repository Maintainers
- **Technical Story**: Card artwork delivery (`public/img/`) and deck shuffling (`src/App.js`)

---

## 1. Context & Problem Statement

Magic Match requires 6 distinct fantasy item illustrations plus a shared card-back cover image, duplicated into a 12-card randomized deck on every new game. We needed a straightforward way to reference card image paths in data structures and generate randomized decks client-side.

---

## 2. Decision

We will serve all card artwork as **static files from `public/img/`** referenced via root-relative paths (`/img/<name>.png`) in `cardImages`, and generate each game's 12-card deck **in-memory** inside `shuffleCards()`.

Implementation Details:
- Static PNG assets live in `public/img/` (`cover.png`, `helmet-1.png`, `potion-1.png`, `ring-1.png`, `scroll-1.png`, `shield-1.png`, `sword-1.png`).
- `shuffleCards()` duplicates the 6-item array (`[...cardImages, ...cardImages]`), shuffles, assigns an `id`, and resets turn state.

---

## 3. Consequences

### Positive Impacts
- **Simple Data Declarations**: Card definitions are plain JSON-compatible objects (`{ src, matched }`) with no static ES `import` boilerplate per image.
- **Fast Local & Static Serving**: Images are copied directly into `build/img/` without webpack base64 inlining bloat.

### Negative Impacts & Operational Costs
- **No Content-Hash Cache Busting**: Files in `public/` bypass webpack's filename hashing (`[contenthash]`), meaning updating an image with the same filename requires cache invalidation or renaming.
- **Root-Path Assumption**: Hardcoded `/img/...` paths assume deployment at domain root (`/`) rather than a subpath (e.g., `/magic-match/`), unless prefixed with `process.env.PUBLIC_URL`.
- **Known Shuffle & ID Limitations (v0.1.0)**:
  - `.sort(() => Math.random() - 0.5)` introduces statistical shuffle bias.
  - `id: Math.random()` has a theoretical (though tiny) key collision risk.

### Risks and Mitigations
- **Risk**: Deploying to a GitHub Pages subpath breaks `/img/*.png` links.
- **Mitigation**: Prefix image URLs with `${process.env.PUBLIC_URL}/img/...` or import assets from `src/assets/` if subpath hosting is required.
- **Risk**: Non-uniform card distribution from comparator-based sort.
- **Mitigation**: Track upgrade to an unbiased Fisher-Yates shuffle and `crypto.randomUUID()` (or deterministic index-based IDs) in a follow-up `refactor(game)` PR.

---

## 4. Alternatives Considered & Disqualification Rationale

### Option A: Webpack ES Module Imports (`src/assets/img/*.png`)
- *Pros*: Automatic content-hash cache busting and subpath-safe URLs.
- *Cons*: Requires explicit `import` statements for every card asset; slightly more verbose for small static demos.

### Option B: External Image CDN / Object Storage
- *Pros*: Offloads static bandwidth and supports dynamic card packs.
- *Cons*: Introduces external runtime availability dependencies and CORS/CSP configuration complexity for 7 small PNGs (~270 KB total).
