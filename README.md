# cla-assistant-action

The CLA gate for `devicecloud-dev/dcd-cli`. Contributors sign by commenting a
phrase on their PR; signatures are stored as JSON on a branch of the *consuming*
repo, so no third-party service holds the data.

Fork of [`contributor-assistant/github-action`](https://github.com/contributor-assistant/github-action)
(Apache-2.0, see `LICENSE`), which its author **archived read-only on 2026-03-23**.
We now maintain this.

## Why we forked

GitHub's Node 20 deprecation began force-running `node20` actions on Node 24.
Upstream still declares `node20`, and under the forced migration the step does its
work, logs `All contributors have signed the CLA ✅`, and **then exits non-zero** —
so a required check goes red having succeeded. That blocked every `dcd-cli` merge
from 2026-09-02 (last green 2026-08-31). Upstream is archived, so no fix was coming.

## What differs from upstream

`action.yml`: `using: "node20"` → `using: "node24"`. That is the entire diff.

`dist/index.js` is **deliberately not rebuilt.** The shipped bundle was verified to
load and execute on Node 24.20.0 — it reaches Octokit's auth check, well past module
load — so the code was never the problem, only the declared runtime. Rebuilding a
1.2 MB `ncc` bundle against a 2024 lockfile, inside a legal-consent gate, for no
demonstrated benefit, is not a trade worth making.

Upstream's own scaffolding was removed: its CI (targeting node 18/20), CodeQL, a
workflow that rewrites this README, issue templates, funding config, docs and
screenshots. Five of six fired and failed on the first push here. `src/`,
`__tests__/`, `package.json` and `tsconfig.json` are kept so a rebuild is possible
(`npm ci && npm run build`).

## Consuming it

```yaml
- uses: devicecloud-dev/cla-assistant-action@v2.6.2
```

This repo is **public**, and has to be: `dcd-cli` is public, and a public repo's
workflow cannot resolve an action from a private one. It was created private and
failed with `Unable to resolve action ... not found` even with the org access
policy set to "Accessible from repositories in the devicecloud-dev organization".
Private buys nothing here anyway — the action's code has to be readable by a
public repo's runs either way, and all of it is Apache-2.0 upstream plus one line.

## Versioning

`v2.6.2` = upstream `v2.6.1` + the runtime declaration. All 32 upstream tags are
mirrored, so `v2.6.1` still resolves for a rollback. Upstream is archived and will
not move again, so there is nothing to sync.
