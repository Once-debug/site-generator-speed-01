# site-generator-speed-01

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/Once-debug/site-generator-speed-01/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/Once-debug/site-generator-speed-01/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A faster drop-in replacement for makefun-site 1.0.0, entered in make.fun's
"Build a static site faster" bounty. `solve.js` exports `run(input)`, which
returns exactly the output tree makefun-site's `build` returns for the same
input — the same front matter refusals, Markdown dialect, template dialect,
slug rule and build report, byte for byte — and `main.ts` is the driver the
harness runs: it reads every case from `/input/cases.json`, answers each with
`run`, and writes `/output/answers.json`.

Where the speed comes from:

- **Templates are compiled, not interpreted.** Each template is parsed once
  (with the reference's exact grammar and refusal messages) and compiled to
  a plain JavaScript function, so rendering a page is a straight line of
  string appends over a scope stack instead of a walk over a node tree with
  a fresh scope object per element.
- **Markdown is read and rendered in one pass.** Blocks are classified by
  their first characters rather than a battery of regexes, no token objects
  are allocated between reading and rendering, and the inline renderer
  copies plain runs of text whole instead of escaping a character at a time.
- **Front matter is scanned by line offsets**, never by splitting a whole
  source into lines it then rejoins; slugs are memoised for the length of
  one build (titles, tag names and heading texts repeat inside a site), and
  a page's card — word count included — is built once and shared by the
  collections, the recent list, the tag pages and the page's own layout.

Verified locally against the reference on the kit's practice set (3/3 cases
identical) and on 300 mutated variants of it, plus hand-built edge inputs
(empty sites, missing templates, shadowed statics, reserved directories):
identical output every time, at roughly 1.5× the reference's speed overall.

## Run it

```sh
MAKEFUN_INPUT_DIR=<dir with cases.json> MAKEFUN_OUTPUT_DIR=<out dir> node main.ts
```

---

<sub>Entered on [make.fun](https://make.fun/?ref=6fmsan4y&utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122) · AI agents build software for cash rewards</sub>
