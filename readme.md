This app is generated with AI tools.

# Wolf Golf Scorer

A lightweight single‑page web app for scoring casual "Wolf" golf games (3–4 players). Built almost entirely through iterative AI assistance plus light human review. Just open `index.html` locally—no build, no dependencies.

## Core Concepts (Wolf Variant Used Here)
Wolf is a hole‑by‑hole points game. Each hole has a designated Wolf. The Wolf chooses (explicitly here by selecting a mode) to:

1. 🔥 Super Wolf (formerly "Lone Wolf"): Go fully alone at double stakes.
2. 🐺 Wolf (standard solo): Go alone at normal stakes.
3. 🤝 Wolf & Partner: Team up with a single partner against the remaining players.

The app normalizes the pot baseline to 1 point. All payouts are expressed relative to that fixed pot.

### Scoring Table (Pot = 1)
| Mode | Situation | Points Awarded |
|------|-----------|----------------|
| Super Wolf | Wolf beats field | Wolf +4 |
| Super Wolf | Single opponent beats Wolf | That opponent +4 |
| Super Wolf | Opponent team beats Wolf | Each opponent +2 |
| Wolf (solo) | Wolf beats field | Wolf +2 |
| Wolf (solo) | Single opponent beats Wolf | That opponent +2 |
| Wolf (solo) | Opponent team beats Wolf | Each opponent +1 |
| Wolf & Partner | Wolf team wins | Wolf +1, Partner +1 |
| Wolf & Partner | Single opponent beats team | That opponent +2 |
| Wolf & Partner | Opponent team beats Wolf team | Each opponent +1 |
| Any Mode | Tie (declared) | No points |

Ties (pushes) give zero to everyone and the hole is just inert in totals.

### 3 vs 4 Player Differences
- 3 Players: Partner mode forms a 2 vs 1. Solo modes pit 1 vs 2.
- 4 Players: Partner mode forms 2 vs 2. Solo modes pit 1 vs 3.

## Wolf Rotation
Two rotation strategies:
- Rolling: Randomly choose the first Wolf, then cycle in table order.
- Random (Balanced): Precomputes a fair distribution so each player appears a near‑equal number of times; then shuffles within those counts.

`wolfHistory` preserves the actual Wolf per hole—editing earlier holes will not corrupt later Wolf counts.

## Features
- Instant scoreboard (always visible after start) with live point totals & Wolf counts.
- Tie‑aware medal emojis (🥇 🥈 🥉) appear only after the first points are earned; tied players share the same medal.
- Mode selection panel (Super Wolf / Wolf / Partner) with contextual opponent & partner labeling.
- Winner selection dynamically adapts to selected mode.
- Full editability: Jump back to any scored hole, change mode or winners, and the app recalculates all downstream totals deterministically.
- Overview panel (📊): Hole matrix with per‑hole mode, Wolf, winners, and per‑hole point deltas. Tap a row to jump & edit.
- Local persistence (`localStorage`) with versioned state object (players, scores, wolf order, wolf history, hole results).
- Custom reset confirmation modal (no accidental nukes).
- Mobile friendly: Sticky header, touch optimized, viewport locking (no pinch zoom jank), compact layout.
- Accessible focus outlines and ESC/backdrop modal closing.

## Data Model (Simplified)
```
{
  players: string[],
  currentHole: number,
  wolfHistory: number[],          // indices into players
  holeResults: [{
    hole: number,
    result: { type: 'wolf-solo' | 'lone-wolf' | 'wolf-partner', wolf, partner?, opponents[] },
    winners: string[],             // [] => tie
    pointsAwarded: { [player]: +points }
  }],
  scores: { [player]: number }
}
```
Scores are always reconstructed via `recalculateScores()` when a past hole is edited (source of truth = `holeResults`).

## Editing Logic
- Selecting a different mode on an already scored hole clears its winners & awaits new selection.
- Switching holes reloads stored mode & winners (with visual highlighting) if present.
- New holes auto‑advance after winner selection; edits do not.

## Persistence
`localStorage` key: `wolfGameState`.
State is saved after most mutations. Reset fully clears it. A version number guards against incompatible future changes.

## Medal Logic
1. Compute max score; if all zero, suppress medals.
2. Sort players by score.
3. Assign rank numbers with ties collapsing (1,1,3 style).
4. Map ranks 1–3 to 🥇 🥈 🥉; tied players share the same emoji.

## Usage
1. Open `index.html` in any modern browser (mobile Safari/Chrome/Edge/Firefox).
2. Enter 3 or 4 player names (blank defaults to "Player X").
3. Choose rotation (Rolling or Random) and start.
4. For each hole: Select a mode, then winner(s).
5. Use 📊 or the hole indicator after the game starts to access the overview.
6. Tap rows in the overview to jump/edit.
7. Reset via the Reset Game button (confirmation dialog appears).

## Assumptions & Simplifications
- Pot fixed to 1 point for clarity and to simplify multipliers.
- No carry-over or escalating stakes logic (can be added if desired).
- No partial hole insertion/deletion—edits only adjust existing holes.
- All players play all holes (no absences / substitutions modeled).

## Potential Enhancements
- Export/import JSON for persistent season tracking.
- Per-hole notes or strokes entry (to later validate winners).
- Optional escalating wolf multipliers on back nine.
- Progressive dark/light mode toggle.
- Cumulative per-hole running totals in the overview.

## Contributing
Open the file, hack, refresh. If adding behavior that changes scoring, ensure `recalculateScores()` mirrors the forward scoring logic exactly.

## Disclaimer
Rule sets for "Wolf" vary by group. This implementation reflects one consistent, explicitly documented variant. Adjust point values or add modes as needed.

Enjoy the round and may your Super Wolf calls pay off.
