# System Design & Component Topology

*Status*: Living Architecture Specification  
*Last Updated*: 2026-11-21

---

## 1. Executive Summary

**Magic Match** is a zero-backend, client-side Single-Page Application (SPA) built on **React 18.2** and **Create React App (`react-scripts` 5.0.1)**. All game logic, deck shuffling, turn tracking, pair comparison, and 3D card-flip animations execute locally in the user's browser with zero network calls after initial static asset delivery.

---

## 2. Deployment & Runtime Topology

```mermaid
graph TD
    subgraph Client["User Browser Runtime"]
        DOM["public/index.html (#root)"]
        ReactRoot["src/index.js (ReactDOM.createRoot + StrictMode)"]
        App["src/App.js (Game State Engine)"]
        Grid["4x3 CSS Grid (.card-grid)"]
        Cards["12x SingleCard Components"]
        CSS["CSS3 3D Flip Engine (SingleCard.css)"]

        DOM --> ReactRoot
        ReactRoot --> App
        App --> Grid
        Grid --> Cards
        Cards --> CSS
    end

    subgraph StaticHost["Static Asset Host / CDN"]
        Bundle["Compiled JS/CSS Bundles (build/static/)"]
        ImgAssets["Card Artwork (public/img/*.png)"]
        Manifest["PWA Manifest & Icons"]
    end

    subgraph External["External Dependencies"]
        GoogleFonts["Google Fonts CDN (Raleway)"]
    end

    StaticHost -->|"HTTPS GET"| Client
    GoogleFonts -->|"CSS @import"| Client
```

---

## 3. Component Hierarchy & Data Flow

Magic Match enforces a strict **unidirectional data flow** (top-down props, bottom-up event callbacks):

```mermaid
flowchart TD
    Index["index.js\nMounts App in React.StrictMode"] --> App["App.js (Stateful Container)\nState: cards, turns, choiceOne, choiceTwo, disabled"]

    App --> Header["Header & Controls\nh1 Magic Match + New Game button"]
    App --> CardGrid["div.card-grid (4 columns, 20px gap)"]
    App --> Footer["Turn Counter\np Turns: N"]

    CardGrid --> SC1["SingleCard #1..#12\nProps: card, handleChoice, flipped, disabled"]
    SC1 -->|"Click on .back when !disabled"| Callback["handleChoice(card)"]
    Callback --> App
    Header -->|"onClick"| Shuffle["shuffleCards()"]
    Shuffle --> App
```

### Component Responsibilities

| Component | File | Type | Responsibilities |
|---|---|---|---|
| **Root Bootstrap** | `src/index.js` | Entrypoint | Creates React 18 root on `#root` and wraps `<App />` in `<React.StrictMode>`. |
| **Game Engine** | `src/App.js` | Stateful Container | Owns deck generation (`shuffleCards`), selection state (`choiceOne`, `choiceTwo`), comparison effect, input lock (`disabled`), and turn counter (`turns`). |
| **Card View** | `src/components/SingleCard.js` | Presentational | Renders `.front` (card face) and `.back` (`cover.png`), applies `.flipped` class, and guards click events against `disabled`. |

---

## 4. Card Selection & Match Resolution Sequence

The following sequence diagram traces both **Match** and **Mismatch** paths from user click through React state updates and `useEffect` reconciliation:

```mermaid
sequenceDiagram
    actor Player
    participant CardA as SingleCard (A)
    participant CardB as SingleCard (B)
    participant App as App.js (State & Effects)

    Player->>CardA: Clicks .back cover
    CardA->>CardA: Guard: !disabled
    CardA->>App: handleChoice(cardA)
    App->>App: setChoiceOne(cardA)
    App-->>CardA: Re-render (flipped = true)

    Player->>CardB: Clicks .back cover
    CardB->>CardB: Guard: !disabled
    CardB->>App: handleChoice(cardB)
    App->>App: setChoiceTwo(cardB)
    App-->>CardB: Re-render (flipped = true)

    Note over App: useEffect([choiceOne, choiceTwo]) triggers
    App->>App: setDisabled(true)

    alt choiceOne.src === choiceTwo.src (Match)
        App->>App: setCards(mark matching src as matched: true)
        App->>App: resetTurn() -> clear choices, turns + 1, disabled = false
        App-->>CardA: Re-render (flipped = true via card.matched)
        App-->>CardB: Re-render (flipped = true via card.matched)
    else choiceOne.src !== choiceTwo.src (Mismatch)
        Note over App: setTimeout(resetTurn, 1000ms)
        App->>App: After 1000ms -> clear choices, turns + 1, disabled = false
        App-->>CardA: Re-render (flipped = false, flips back)
        App-->>CardB: Re-render (flipped = false, flips back)
    end
```

---

## 5. Turn State Machine

```mermaid
stateDiagram-v2
    [*] --> Ready: Initial mount useEffect -> shuffleCards()

    Ready --> FirstCardFlipped: handleChoice(cardA)\n[choiceOne = cardA]
    FirstCardFlipped --> Comparing: handleChoice(cardB)\n[choiceTwo = cardB, disabled = true]

    Comparing --> MatchCommitted: choiceOne.src === choiceTwo.src\n[cards[].matched = true]
    Comparing --> MismatchWait: choiceOne.src !== choiceTwo.src\n[1000ms timer active, board locked]

    MatchCommitted --> Ready: resetTurn()\n[choices = null, turns++, disabled = false]
    MismatchWait --> Ready: Timer expires -> resetTurn()\n[choices = null, turns++, disabled = false]

    FirstCardFlipped --> Ready: New Game clicked -> shuffleCards()
    Ready --> Ready: New Game clicked -> shuffleCards()
```

---

## 6. CSS 3D Card Flip Mechanics

Card flips in `src/components/SingleCard.css` are driven purely by CSS `transform: rotateY(...)` and staggered `transition-delay` values—requiring zero JavaScript animation libraries:

```mermaid
flowchart LR
    subgraph FlipOpen["Flipping Face-Up (.flipped added)"]
        direction LR
        O1["0.0s - 0.2s\n.back rotates 0deg -> 90deg\n(delay: 0s)"] --> O2["0.2s - 0.4s\n.front rotates 90deg -> 0deg\n(delay: 0.2s)"]
    end

    subgraph FlipClose["Flipping Face-Down (.flipped removed)"]
        direction LR
        C1["0.0s - 0.2s\n.front rotates 0deg -> 90deg\n(delay: 0s)"] --> C2["0.2s - 0.4s\n.back rotates 90deg -> 0deg\n(delay: 0.2s)"]
    end
```

| Selector | Default Transform | Flipped Transform | Transition Duration | Transition Delay |
|---|---|---|---|---|
| `.card .front` | `rotateY(90deg)` (hidden edge-on) | `rotateY(0deg)` (visible) | `0.2s ease-in` | `0s` (closing) / `0.2s` (opening) |
| `.card .back` | `rotateY(0deg)` (visible) | `rotateY(90deg)` (hidden edge-on) | `0.2s ease-in` | `0.2s` (closing) / `0s` (opening) |

---

## 7. Known Architectural Constraints & Improvement Roadmap

Documented honestly from the current implementation (`v0.1.0`):

1. **Shuffle Bias**: `shuffleCards()` currently uses `.sort(() => Math.random() - 0.5)`, which produces a non-uniform distribution across JS engines. *Recommended upgrade*: Fisher-Yates shuffle.
2. **Timer Cleanup on Reset**: Clicking **New Game** during a 1000ms mismatch delay does not cancel the pending `setTimeout`, which could increment `turns` on the new game. *Recommended upgrade*: Store timeout ID in a `useRef` or return a cleanup function from `useEffect`.
3. **Diagnostic Logging**: `src/App.js` currently logs `console.log(cards)` on every render. *Recommended upgrade*: Remove before production release.
4. **Placeholder Unit Test**: `src/App.test.js` still contains the default Create React App test (`/learn react/i`). *Recommended upgrade*: Replace with game-behavior tests (grid renders 12 cards, matching pair increments turns, New Game resets turns).
5. **Victory Detection**: No explicit game-completion banner when all 6 pairs are matched (`cards.every(c => c.matched)`).
