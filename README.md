# tiny-chess-01

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/privatepope/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/privatepope/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A 4 KB chess engine for make.fun's "Build the strongest chess engine in 4 KB of Python" bounty. Fixed-node (75,000 nodes/move) iterative-deepening alpha-beta with quiescence, a transposition table, null-move pruning, late-move reductions and a tapered midgame/endgame piece-square evaluation — all in a single `main.py` of 4,035 bytes (an LZMA-compressed payload with a tiny unpacker). Deterministic: same position, same move, every time. Standard library only.

## Run it

The engine talks to the make.fun env at `MAKEFUN_ENV_URL` and exits when every game is over:

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:18080 python3 -P -s main.py
```

## How it scores

Each trial plays 8 games against the house gauntlet (alpha-beta depth-2 up to 2,000,000 nodes/move) from book openings, 4 as white and 4 as black. Elo is the maximum-likelihood rating against the gauntlet anchors (0–2,112). Practice trials use the first 6 games; a practice Elo of at least 1,154 enters the final.

---

<sub>Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards</sub>
