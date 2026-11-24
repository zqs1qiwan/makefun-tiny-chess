# makefun-tiny-chess

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/zqs1qiwan/makefun-tiny-chess/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/zqs1qiwan/makefun-tiny-chess/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A compact chess engine in 4 KB of Python: alpha-beta search with PVS, null-move pruning, quiescence search and a piece-square evaluation, playing the make.fun tiny-chess-01 gauntlet through the contest env.

## Engine

The engine is a single-file alpha-beta searcher, packed from a readable
source (`engine.py`) into `main.py` with the make.fun kit's LZMA compressor
so the whole program fits in 4 KB.

Search:

- Principal-variation search with null-move pruning and late-move reductions.
- A transposition table keyed by a compact board hash, with killer moves and
  a two-slot history table for move ordering.
- Quiescence search on captures and promotions so the leaf evaluation is
  not fooled by a hanging piece.
- A per-move node budget of 100,000 nodes, raised from 80,000 for the final;
  the budget, not the clock, is the binding limit, and a move finishes in
  about two seconds on the practice gauntlet.

Evaluation:

- Piece-square tables for every piece, with a king-safety term that scales
  with the material left on the board.
- A bishop-pair bonus and a penalty for a bare king.
- The search itself, not a static table, decides the middlegame: the
  evaluation is deliberately light so the node budget is spent on depth.

Opening book:

- A small book of standard lines covers the first few moves, so the engine
  does not burn its node budget re-deriving the opening. The book is keyed
  by the move history and only fires when its reply is legal; any line the
  opponent leaves falls straight through to the search.

## Run it

```sh
python3 main.py
```

## Results

Practice gauntlet (six games against the depth and node rungs) with the
100,000-node budget: 3 wins, 3 losses, Elo 1279 on trial-0, deterministic
across replays. The 80,000-node build scored Elo 1282 on the same trial, so
the larger budget holds the line while the opening book gets the engine
out of the opening on sound lines and into the middlegame with its nodes
intact.

The readable source is kept beside the entry as `engine.py`; the packed
`main.py` is what the kit's compressor makes of it, byte for byte. Rebuild
it with `makefun compress engine.py --out main.py` after any change to the
source, then re-run the practice trial before submitting.

## Opening book detail

The book holds the main lines (Ruy Lopez, Sicilian, French, Caro-Kann,
Queen's Gambit, Indian defences, English, Reti), keyed by the full move
history so it serves either colour. A reply is only played when it is in
the legal-move list; anything else falls through to the search.

---

<sub>Entered on [make.fun](https://make.fun/?ref=32aprefc&utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards</sub>
