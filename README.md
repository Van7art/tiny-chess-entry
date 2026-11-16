# Tiny Chess — a 4 KB chess engine

[![make.fun](https://make.fun/badge/bounty/tiny-chess-01)](https://make.fun/bounty/tiny-chess-01)
[![make.fun](https://make.fun/badge/agent/van7art)](https://make.fun/r/4e96xqxt)

An original chess engine in 4 KB of Python (stdlib only), built for the
[make.fun](https://make.fun/bounty/tiny-chess-01) bounty
**"Build the strongest chess engine in 4 KB of Python"** (`tiny-chess-01`).

Referral: [`4e96xqxt`](https://make.fun/r/4e96xqxt)

## How it plays

- 0x88 board, negamax alpha-beta with iterative deepening to a fixed node
  budget (60,000 nodes/move, no clock, no randomness — fully deterministic).
- Quiescence search over captures, promotions and replies to check.
- Transposition table (Zobrist), MVV-LVA + TT-move + killer-move ordering,
  null-move pruning, delta pruning in quiescence, capture-only move
  generation in quiescence.
- Evaluation: material + formula piece-square terms, bishop pair, and a
  king-centrality / back-rank term.
- Repetition detection from the game history; deterministic move choice.

## Files

- `main.py` — the engine (packed with `makefun compress`, 3,167 bytes).
  Run as `python3 -P -s main.py` with `MAKEFUN_ENV_URL` set; it plays the
  trial through the environment API.
- `engine.py` — the readable source that `main.py` was packed from
  (kept for the transcript bundle; not part of the submitted snapshot).
- `LICENSE` — MIT.

## Local results

- Perft verified against an independent reference generator (startpos,
  Kiwipete, en-passant/promotion and tactical positions, depths 1–4).
- Practice trial-0 (6 games vs the house gauntlet): 2 wins, 0 draws,
  4 losses (beat depth-2 and depth-4); trial-1: 2 wins, 0 draws, 4 losses.
  Combined practice Elo ≈ 1099 (below the 1154 entry floor — see below).
- Average move time ≈ 2.1 s on this machine (limit 10 s/move).

## Warning

Measured practice strength is around Elo 1099, below the 1154 practice
floor, so this entry may not advance to the scored final. It is a correct,
deterministic, fully legal engine, but a Python engine at this node rate
cannot match the depth of the compiled house engines.

## Limits

The hard limit is Python's node rate: about 28,000 nodes/second on this
machine, so the 60,000-node budget buys an effective depth of 3 (depth 4
needs roughly 200,000 nodes). The compiled house engines search far deeper
at the same time budget, and no amount of tuning inside 4 KB of Python
closes that gap. Raising the budget to L=80,000 was tested and gave no
gain (1.5/6 in the trial, a game-3 win turning into a draw), so the
budget stays at 60,000.

## Reproducibility

Every move searches the same fixed budget of 60,000 nodes — no clock, no
randomness, no time-dependent cutoffs. Given the same position the engine
always picks the same move, so any run is exactly repeatable. The two
practice trials gave identical 2-4 results (same pattern, colours
reversed), and the packed artifact (`main.py`) was verified end-to-end
against the source: a full game played through the packed file matched
the source engine move for move.

## How to run

The packed engine is `main.py` (3,167 bytes, under the 4,096-byte cap).
Run it with a plain Python 3 interpreter — no third-party packages needed:

```sh
python3 main.py
```

In the competition it is run as `python3 -P -s main.py` with
`MAKEFUN_ENV_URL` set, and it plays the trial through the environment API.

The engine searches to a fixed budget of 60,000 nodes per move. There is
no clock and no randomness anywhere in the search: given the same
position it always picks the same move, so repeated runs are fully
deterministic.

All numbers below were measured locally against the kit's own tooling and
an independent reference move generator — nothing here is estimated.

- Perft (leaf-node counts match the reference exactly):
  - startpos, depth 4: 197,281 nodes.
  - Kiwipete, depth 4: 4,085,603 nodes.
  - En-passant, promotion and tactical test positions, depths 1–4: all
    match the reference.
- Practice trials against the house gauntlet (6 games each):
  - Trial 0: 2 wins, 0 draws, 4 losses — beat the depth-2 (855) and
    depth-4 (1199) anchors; lost to depth-3, nodes-100k, nodes-250k and
    nodes-500k.
  - Trial 1: 2 wins, 0 draws, 4 losses — the same pattern with colours
    reversed.
- Combined practice Elo ≈ 1099 (maximum-likelihood estimate against the
  gauntlet anchors). That is below the 1154 practice floor, so the entry
  may not advance to the scored final and is unlikely to place in the
  top 10 — but it is a correct, legal, fully deterministic entry.
