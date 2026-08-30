### Changed

#### `.standards` advanced to the current Go standards, and Dependabot now keeps it there

`changelog.d/20260810-bump-standards.md` moved this pointer three weeks ago and
closed with "the pointer moves so a fresh clone here gets the corrected
instruction files." It has not moved since, and upstream has not stood still: six
commits landed in `falkcorp/.github` after `5a59803`, and this bumps the pin to
`7bdfd13` to pick them all up.

A git submodule is a **pinned commit, not a live link**. `CLAUDE.md` names
`.standards/instructions/` authoritative and sends contributors and agents there,
so a stale pin silently serves outdated rules rather than failing loudly. The pin
served `instructions/go.md` at **version 1.0.0, 63 lines**, while upstream had
reached **1.3.0, 384 lines**. The gap is exactly the content people are told to
follow:

- the Go version policy and the 1.26 minimum, with 1.27 preferred where
  dependencies allow, and the two named 1.27 blockers
- the `io/ioutil` ban and the rest of the deprecated-stdlib table
- the `wg.Go(fn)` rule — never `Add(1)` plus `defer Done()`
- the testing-isolation table (`t.Setenv`, `t.Chdir`, `t.TempDir`, `synctest`)
- `omitempty` versus `omitzero` under `encoding/json/v2`
- the rule that naming an external blocker requires a working control first

None of it was reaching anyone. Every rule was merged upstream and then read by
no consumer, because the pin is what the repository actually checks out.

The reason it went stale is that **there is no sync automation anywhere in the
org** — the pointer has only ever moved when someone remembered. So this adds a
`gitsubmodule` ecosystem to `.github/dependabot.yml`, on the same weekly
`multi-ecosystem-group` as Cargo and Actions. Falling behind now opens a pull
request. Bumping the pin without that would leave the same drift to reappear next
month, which is what happened to the 2026-08-10 bump.

Nothing in this repository *consumes* `.standards`. `ci.yml` checks out
`submodules: recursive` and runs `markdownlint-cli2`, so the submodule's files do
reach CI's working tree — but the only reference to them anywhere in the build is
the `".standards/**"` entry in `.markdownlint-cli2.jsonc`, which is an
**exclusion**: that content belongs to `falkcorp/.github` and is linted there. The
bump therefore cannot change a lint verdict here, and it changes no Rust code. It
is documentation and templates, as before.
