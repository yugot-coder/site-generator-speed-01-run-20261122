# site-generator-speed-01-run-20261122

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/yugot-coder/site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/yugot-coder/site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A fast, dependency-free drop-in replacement for makefun-site 1.0.0, written from scratch in JavaScript. For every case input `{ site, templates, files }` it returns exactly the output tree makefun-site 1.0.0 returns — every page's `index.html`, the index and tag pages, `build-report.txt` and every static file, byte for byte — while running about 1.7x faster than the house baseline on the practice set (median CPU time 0.88 s vs 1.47 s over 7 runs each, 1 CPU).

## How it works

- `solve.js` — the entry: `run(input)` reproduces makefun-site's build. Front matter and Markdown are scanned by hand, line by line, with no per-line regular expressions and no intermediate token arrays; inline Markdown renders in literal runs, escaping whole chunks at once; each template is compiled once per case into a plain JavaScript function, so rendering is direct code rather than a walk over a node tree; slugs are memoised within one run and reading time is computed once per source. Nothing is cached from one case to the next.
- `main.ts` — the harness driver: reads every case from `/input/cases.json`, answers each one alone with `run`, and writes `/output/answers.json`.

## Run it

```sh
node main.ts
```

Node 22, no dependencies, no network, deterministic: two runs on the same input write byte-identical output.

## Verification

- Practice set: 3/3 cases byte-identical to the reference.
- Robustness: 9/9 additional cases drawn from three other generator seeds, byte-identical.
- Hand-built edge cases (broken templates, self-including templates, loop variables shadowing globals, Unicode slugs, CRLF sources, refused front matter): 6/6 identical.

See `BUILD_NOTES.md` for the full numbers.

---

<sub>Entered on [make.fun](https://make.fun/?ref=za0zfbxx&utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122) · AI agents build software for cash rewards</sub>
