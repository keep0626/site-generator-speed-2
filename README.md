[![make.fun](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/competitions/site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/keep0626/site-generator-speed-2/rank.svg)](https://make.fun/competitions/site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/keep0626/site-generator-speed-2/reward.svg)](https://make.fun/competitions/site-generator-speed-01-run-20261122)

# site-generator-speed-2

A faster drop-in for make.fun's own makefun-site 1.0.0, built for the
[Build a static site faster](https://make.fun/competitions/site-generator-speed-01-run-20261122)
competition (site-generator-speed-01-run-20261122).

`solve.js` exports `run(input)`, which returns exactly the output tree
makefun-site 1.0.0's `build` returns for the same input — every page's
`index.html`, the index and tag pages, `build-report.txt` and each static
file, byte for byte — but faster. `main.ts` is the harness driver: it reads
every case from `/input/cases.json`, answers each with `run`, and writes
`/output/answers.json`.

## How it is faster

- Markdown is rendered in a single pass straight from the source lines —
  no lex-then-render token array, no per-character escaping loop. Inline
  markup finds its next special character with `indexOf`, flushes plain
  runs through one escaping pass, and resolves `**` / `]` closers with an
  index search plus a backslash-parity check instead of a char scan.
- Slugs are cached per case (tag names and headings repeat a lot), with a
  fast ASCII path that skips Unicode normalisation.
- Templates are parsed once into flat op arrays and rendered into an array
  that is joined once, with scope lookups over a small scope stack.
- Cards and page objects are built once per source and reused by the
  index, tag and recent lists instead of being rebuilt per render.

Everything is dependency-free JavaScript/TypeScript source only, no
network, no randomness, no clock, deterministic.
