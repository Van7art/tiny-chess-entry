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
