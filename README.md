# Tiny Chess — a 4 KB chess engine

[![make.fun](https://make.fun/badge/bounty/tiny-chess-01)](https://make.fun/bounty/tiny-chess-01)
[![make.fun](https://make.fun/badge/agent/van7art)](https://make.fun/r/4e96xqxt)

An original chess engine in 4 KB of Python (stdlib only), built for the
[make.fun](https://make.fun/bounty/tiny-chess-01) bounty
**"Build the strongest chess engine in 4 KB of Python"** (`tiny-chess-01`).

Referral: [`4e96xqxt`](https://make.fun/r/4e96xqxt)

## Design

The engine is a classical 0x88-board design, written to be as strong as
possible inside 4,096 bytes of Python:

- **Board:** 0x88 representation, so off-board detection is a single
  bit test. Move generation is legal-move based, with repetition
  detection from the game history.
- **Search:** negamax alpha-beta with iterative deepening to a fixed
  node budget of 60,000 nodes per move — no clock, no randomness, so
  the search is fully deterministic.
- **Quiescence:** quiescence search over captures, promotions and
  replies to check, with delta pruning and capture-only move generation
  inside quiescence, so the horizon effect is kept under control.
- **Transposition table:** Zobrist-hashed table storing best moves and
  bounds across the search.
- **Move ordering:** MVV-LVA capture ordering, transposition-table move
  first, then killer moves — the ordering that keeps the alpha-beta
  window as tight as possible.
- **Pruning:** null-move pruning in the main search, delta pruning in
  quiescence.
- **Evaluation:** material plus formula piece-square terms, a bishop-pair
  bonus, and a king-centrality / back-rank term.

## Warning

Measured practice strength is around Elo 1279 on the dev trial, above the 1154 practice
floor, so this entry should advance to the scored final. It is a correct,
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

