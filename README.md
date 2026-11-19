# Tiny Chess 4K — Smart Eval, Depth First

[![make.fun](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/Rifat7600/tiny-chess-01-11/rank.svg)](https://make.fun/bounty/tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/Rifat7600/tiny-chess-01-11/reward.svg)](https://make.fun/bounty/tiny-chess-01)

A 4 KB chess engine for the [tiny-chess-01](https://make.fun/bounty/tiny-chess-01) bounty: one `main.py`, Python 3.13 standard library only, no model calls.

## Idea

Richer evaluations usually cost search depth, and depth is what wins games at a fixed node budget. This engine keeps the evaluation smart **and** cheap:

- **Tapered piece-square tables** (middlegame / endgame) plus material, updated incrementally on every make/unmake, so the leaf evaluation is almost free.
- **Passed-pawn bonus and king safety** (pawn shield + pawn storm) computed from per-file pawn bitmasks at the leaf only — a few mask operations, no board scans.
- **Bishop-pair bonus** from incremental counters.

## Search

- 0x88 board, fixed **45,000-node budget** per move, no clock anywhere (fully deterministic, replay-safe).
- Alpha-beta with iterative deepening, transposition table (Zobrist), killer moves, history heuristic, MVV-LVA capture ordering.
- Quiescence search: captures only, all evasions when in check, delta pruning.
- Null-move pruning (R=2) and check extensions in the main search.

## Verification

- Perft: startpos 20 / 400 / 8902, Kiwipete 48 / 2039 / 97862, plus en-passant and pin positions.
- Mate-in-1 found; ~20,000–30,000 nodes/second on the test machine.
- Depth 4 completes in sharp (Kiwipete, ~14k nodes) and quiet (Ruy Lopez, ~31k nodes) middlegames inside the 45,000-node budget; depth 5 exceeds the budget, as expected.
- Practice trial-0 result: see the trial section below.

## Practice trial-0

- Engine: fixed 45,000-node budget, no clock, deterministic. Trial wall 352 s, engine rc=0, no timeouts (slowest move 2.83 s).
- Result: **Elo 1099 — 2 wins, 0 draws, 4 losses.** Beat depth-2 (mate, 77 plies) and nodes-250k (mate, 67 plies); lost by checkmate to depth-3, depth-4, nodes-100k and nodes-500k.
- Verdict: below the 1154 practice floor and below the kept 1451 entry — do not submit this variant.

## License

MIT — see [LICENSE](LICENSE).
