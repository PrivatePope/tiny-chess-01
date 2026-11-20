# tiny-chess-01

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/privatepope/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/privatepope/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A 4 KB chess engine for make.fun's "Build the strongest chess engine in 4 KB of Python" bounty. Fixed-node (100,000 nodes/move) iterative-deepening alpha-beta with quiescence, a transposition table, null-move pruning, late-move reductions and a tapered midgame/endgame piece-square evaluation — all in a single `main.py` of 3,280 bytes (an LZMA-compressed payload with a tiny unpacker, packed with the kit's own `makefun compress`). Deterministic: same position, same move, every time. Standard library only.

## How it works

The board is a 0x88 array of 128 squares, copied on every move instead of unmade, so the search never has to undo anything. Move generation walks the board once per node and scores captures by victim value, which keeps the ordering cheap and the node rate high.

Search runs iterative deepening to a fixed budget of 100,000 nodes per move, with no clock anywhere, so the same position always produces the same move. Inside the search: alpha-beta with a quiescence search over captures, promotions and check evasions; a transposition table keyed on the board, side, castling rights and en passant square; null-move pruning once the position is developed enough; late-move reductions for quiet moves late in the ordering; and an extra ply when the side to move is in check.

Evaluation is a tapered blend of midgame and endgame piece-square tables, decoded from a single letter string at import time. Material, square bonuses and game phase are tracked incrementally as pieces move, so scoring a position is a few arithmetic operations. There is no randomness, no opening book and no endgame tablebase in the engine itself; the book openings come from the match harness.

Packing is the kit's own compressor: the readable source lives beside the entrypoint as `engine6_shrunk_min.py` and `makefun compress` turns it into the 3,280-byte `main.py` the grader runs. The source is the golfed form of a longer engine written during the build; nothing in it was copied from another entry.

Local practice against the kit's own trial harness gave a mean Elo of 1,336 across three trials (1,279 / 1,451 / 1,279), above the 1,154 bar for the final and just under the sunfish baseline of about 1,365. Perft counts match the reference values on all six test positions, and the engine picks the same move twice from the same position at the full node budget.

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
