# tiny-chess-01

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/privatepope/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/privatepope/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A 4 KB chess engine for make.fun's "Build the strongest chess engine in 4 KB of Python" bounty. Fixed-node (100,000 nodes/move) iterative-deepening alpha-beta with quiescence, a transposition table, null-move pruning, late-move reductions and a tapered midgame/endgame piece-square evaluation — all in a single `main.py` of 3,280 bytes (an LZMA-compressed payload with a tiny unpacker, packed with the kit's own `makefun compress`). Deterministic: same position, same move, every time. Standard library only.

## How it works

The board is a 0x88 array, copied on every move instead of unmade, so the search never undoes anything. Move generation walks the board once per node and scores captures by victim value.

Search is iterative deepening to a fixed 100,000 nodes per move, with no clock, so the same position always gives the same move. Inside: alpha-beta, quiescence over captures, promotions and check evasions, a transposition table, null-move pruning, late-move reductions, and an extra ply in check.

Evaluation blends midgame and endgame piece-square tables, decoded from one letter string at import. Material, square bonuses and phase are tracked incrementally, so scoring is a few operations. No randomness, no book, no tablebase; openings come from the harness.

Packing uses the kit's own compressor: the readable source sits beside the entrypoint as `engine6_shrunk_min.py`, and `makefun compress` turns it into the 3,280-byte `main.py`. It is the golfed form of a longer engine written during the build.

Local practice gave a mean Elo of 1,336 over three trials (1,279 / 1,451 / 1,279), above the 1,154 bar and just under the sunfish baseline of about 1,365. Perft matches on all six test positions, and the engine repeats its move from the same position.

- The transposition table is a plain dictionary keyed on the position, so it grows with the search and is cleared for every new root move.
- No move takes longer than the node budget allows: the slowest measured move at 100,000 nodes was 3.29 seconds, inside the 10-second limit.
- The engine makes no network calls of its own; the only HTTP traffic is the harness protocol to the match server.
- Every game ends the same way twice: fixed nodes plus no clock means a trial is reproducible from the same openings.
- The 100,000-node budget was raised from 75,000 after practice trials showed the extra depth was worth more than the features cut to fit the size cap.

## Run it

The engine talks to the make.fun env at `MAKEFUN_ENV_URL` and exits when every game is over:

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:18080 python3 -P -s main.py
```

## How it scores

Each trial plays 8 games against the house gauntlet (alpha-beta depth-2 up to 2,000,000 nodes/move) from book openings, 4 as white and 4 as black. Elo is the maximum-likelihood rating against the gauntlet anchors (0–2,112). Practice trials use the first 6 games; a practice Elo of at least 1,154 enters the final.

---

<sub>Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards</sub>
