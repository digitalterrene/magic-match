# ADR-002: Pure CSS 3D `rotateY` Card Flip Animations

- **Status**: Accepted
- **Date**: 2026-11-21
- **Authors**: Core Engineering Team
- **Deciders**: Repository Maintainers
- **Technical Story**: Card flip visual transitions (`src/components/SingleCard.css`)

---

## 1. Context & Problem Statement

A memory card game relies on clear, responsive visual feedback when cards flip face-up and face-down. We needed a smooth 3D card-flip animation that works reliably across modern browsers without bloating the JavaScript bundle or coupling animation frame loops to React state.

---

## 2. Decision

We will implement card flip animations using **pure CSS3 3D transforms (`transform: rotateY(90deg)`) and staggered `transition-delay` timings** toggled by a single `.flipped` class in `src/components/SingleCard.css`.

Implementation Details:
- `.card .front` is positioned `absolute` at `rotateY(90deg)` (invisible edge-on) by default.
- Adding `.flipped` rotates `.back` from `0deg` to `90deg` over `0.2s` (`delay: 0s`), followed by `.front` rotating from `90deg` to `0deg` over `0.2s` (`delay: 0.2s`).
- Removing `.flipped` reverses the sequence cleanly (`front` closes first, then `back` opens).

---

## 3. Consequences

### Positive Impacts
- **Zero JS Runtime Cost**: Animations execute on the browser compositor thread via CSS transforms.
- **Tiny Bundle Footprint**: Avoids adding 30–50 KB+ of animation library dependencies.
- **Declarative Binding**: React only toggles a single CSS class (`flipped ? "flipped" : ""`).

### Negative Impacts & Operational Costs
- **Fixed Timing Coupling**: Changing animation duration requires keeping `.front` and `.back` `transition-delay` values synchronized manually in CSS.
- **No Spring Physics**: Limited to CSS cubic-bezier easing (`ease-in`) rather than interruptible spring physics.

### Risks and Mitigations
- **Risk**: Rapid clicking during the `0.2s` flip animation could trigger double-selection of the same card.
- **Mitigation**: `disabled` locks clicks during pair evaluation; future hardening should also guard `card !== choiceOne` in `handleChoice`.

---

## 4. Alternatives Considered & Disqualification Rationale

### Option A: Framer Motion / React Spring
- *Pros*: Rich spring physics, gesture support, and declarative mount/unmount animations.
- *Cons*: Adds significant bundle size and dependency maintenance overhead for a single binary flip transition.

### Option B: Instant Visibility Toggle (`display: none` / conditional JSX)
- *Pros*: Simplest possible implementation.
- *Cons*: Jarring user experience; lacks tactile card-flip affordance essential to a memory game.
