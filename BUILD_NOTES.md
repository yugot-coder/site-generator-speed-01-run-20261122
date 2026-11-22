# Build notes — site-generator-speed-01-run-20261122

## Entry

`solve.js` (ES module, dependency-free) + `main.ts` (harness driver). Written from scratch in the post-open authoring session; it reproduces makefun-site 1.0.0's behaviour (front matter, template dialect, Markdown subset, slug rule, report) byte for byte, it does not copy the vendor's code.

## What was optimised

- Front matter: hand-scanned lines, no per-line regexes; body taken as a substring, not split-and-joined.
- Markdown: single pass over lines, no intermediate token array; inline rendering jumps between markup characters with `indexOf` and escapes literal runs in one chunk; `**` / `]` / single-`*` closers are found with `indexOf` plus a backslash-parity check (equivalent to the reference's escape-skipping scan).
- Templates: each template is compiled once per case into a plain JS function (`new Function`); paths are hoisted as constants; rendering appends to a chunk array joined once.
- Slugs memoised within one run (titles/tags repeat); reading time computed once per source; word counting is a single scan; `\r` normalisation skipped when the file has no `\r`.
- `main.ts` writes `answers.json` by joining each file's own `JSON.stringify` — verified byte-identical to `JSON.stringify(answers)`.
- Nothing is cached from one case to the next.

## Verification

- Practice set (kit `practice/`): 3/3 cases byte-identical to the reference.
- Robustness: 9/9 extra cases from three other generator seeds (aaaa…, bbbb…, 12345678…), byte-identical to the oracle.
- Hand-built edge cases: 6/6 identical (broken templates of every kind, self-including template at the depth cap, loop variable named `loop`, Unicode slugs, CRLF sources, refused front matter, empty sites).
- Determinism: two runs on the practice set write byte-identical `answers.json`.

## Speed (1 CPU, `taskset -c 0`, CPU time via child rusage, 7 runs each)

- Entry: median 0.876 s (range 0.830–1.047 s)
- House baseline: median 1.474 s (range 1.418–1.593 s)
- Speedup: ~1.68x

In-process `run()` only (warm): entry ~0.31 s for the 3 practice cases vs baseline ~0.64 s (~2.1x); the rest of the end-to-end time is Node startup plus JSON parse/serialise of the 3 MB input / 7.4 MB output, which both entries pay.

## Repo

Total size well under the 4 MiB cap (solve.js ~36 KB, everything else small). No kit files are committed.
