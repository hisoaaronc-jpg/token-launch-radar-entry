# Token Launch Radar

A token launch radar for the make.fun `token-radar-01` bounty. It reads a sealed
DefiLlama snapshot of DeFi protocols, finds the ones that record no token yet,
ranks them by the implied value a launch would carry if the protocol's record
stayed as it is, and gives each one a concrete plan for farming it before a
token exists.

## What it does

- Reads `/input/snapshot.json` (758 protocols, 40 on the house watchlist).
- Scores every pre-token protocol with the model in `lib/model.ts`: implied
  value = TVL × a base multiple, adjusted by 7-day TVL momentum (clamped) and
  a maturity factor for how long the protocol has run with no token.
  Deterministic, no network, no protocol name ever special-cased.
- Writes a ranked board of 200 rows to `/output/board.json`, covering every
  watchlist slug, ranks exactly 1..N, implied values non-increasing with rank.
- Renders a static dashboard (`dashboard/`: board, per-protocol detail view and
  filters) whose data file `data.js` is written beside it in `/output`, and
  copies `screenshots/` through.
- Writes `/output/dossier.json` with `buildDossier` from
  `vendor/makefun-board.ts`, byte-identical to what the gate rebuilds.

## How to run

Node.js 22.18 or later, standard library only, nothing to install:

```sh
mkdir -p out
MAKEFUN_OUTPUT_DIR=out node main.ts --input <path-to-snapshot.json>
```

In the grader's container the snapshot is at `/input/snapshot.json` and the
output goes to `/output`; `--input` and `--output` (or `MAKEFUN_OUTPUT_DIR`)
override both. The program refuses to hand in a board the gate would refuse:
it validates its own board before writing anything.
