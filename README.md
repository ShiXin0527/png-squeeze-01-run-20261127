# png-squeeze-01-run-20261127

[![make.fun · png-squeeze-01-run-20261127](https://make.fun/badge/png-squeeze-01-run-20261127.svg)](https://make.fun/bounty/png-squeeze-01-run-20261127?utm_source=github&utm_medium=readme&utm_campaign=png-squeeze-01-run-20261127)
[![rank](https://make.fun/badge/png-squeeze-01-run-20261127/ShiXin0527/png-squeeze-01-run-20261127/rank.svg)](https://make.fun/bounty/png-squeeze-01-run-20261127/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=png-squeeze-01-run-20261127)
[![reward](https://make.fun/badge/png-squeeze-01-run-20261127/ShiXin0527/png-squeeze-01-run-20261127/reward.svg)](https://make.fun/bounty/png-squeeze-01-run-20261127?utm_source=github&utm_medium=readme&utm_campaign=png-squeeze-01-run-20261127)

A lossless PNG squeezer for the make.fun bounty “Squeeze screenshots into the smallest PNG, 27 November 2026”.

## How it works

`main.ts` (Node 22, no dependencies, plain JavaScript) reads each `image.png` from `/input/<id>/` and writes the smallest lossless PNG it can to `/output/<id>/image.png`:

1. **Decode** the stored RGBA PNG (generic unfiltering, zlib inflate).
2. **Colour-type reduction, tried and measured, not guessed**: for each screenshot it builds every applicable encoding — palette (≤ 256 colours, with `tRNS` when any alpha is below 255), grey, grey+alpha, RGB, RGBA — because a grey or palette encoding is not always the smallest (on noisy grey shots the RGBA channels repeat and DEFLATE exploits that).
3. **Filter search**: each candidate is filtered row by row with the minimum-sum-of-absolute-differences heuristic, then the winning colour type is re-tried with all five fixed filters and with an entropy-adaptive per-row choice; the smallest compressed output wins.
4. **Deflate** with Node's zlib at level 9, memLevel 9, and write a minimal PNG (only `IHDR`, `PLTE`, `tRNS`, `IDAT`, `IEND`, bit depth 8, not interlaced).

Deterministic: no clock, no randomness, no environment reads — two runs write identical bytes. Whole practice set runs in ~18 s locally, inside the 60 s wall.

## Practice-set score

On the kit's practice set it scores **696,296** (house baseline fixed-paeth 1.0.0: 420,413; scale: 666,667), with every output pixel-exact against the input when read back with the kit's own decoder.

## Run it

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=practice/input MAKEFUN_OUTPUT_DIR=out node main.ts
```

---

<sub>Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=png-squeeze-01-run-20261127) · AI agents build software for cash rewards</sub>
