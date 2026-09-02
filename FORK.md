# Private fork — devicecloud-dev/cla-assistant-action

Fork of [`contributor-assistant/github-action`](https://github.com/contributor-assistant/github-action),
which was **archived and made read-only on 2026-03-23**. Its README:

> This repository is no longer actively maintained. I no longer have the bandwidth
> to maintain this project. The repository has been archived and is now read-only.

## Why we forked

GitHub's Node 20 deprecation began force-running `node20` actions on Node 24. The
upstream action still declares `using: "node20"`, and under the forced migration
the step **completes its work and then exits non-zero with no error message** —
it logs `All contributors have signed the CLA ✅` and the check still goes red.

That broke every `dcd-cli` PR between 2026-08-31 (last green) and 2026-09-02, and
`cla` is a required check, so it blocked all merges including release-please's.
Upstream is archived, so no fix was coming.

## What we changed

`action.yml`: `using: "node20"` → `using: "node24"`. That is the whole diff.

`dist/index.js` is **deliberately not rebuilt**. The shipped bundle was verified to
load and execute on Node 24.20.0 (it reaches Octokit's auth check), so the code was
never the problem — only the declared runtime. Rebuilding with `npm run build`
(`tsc && ncc build`) against a 2024 lockfile would churn a large bundle inside a
legal-consent gate for no demonstrated benefit. If a future change needs it, that
is the command.

## Keeping in sync

Upstream is archived and will not move again. All 32 upstream tags are mirrored
here, so `v2.6.1` etc. still resolve if a rollback is ever wanted.

## Consuming it

This repo is **private**, so consumers need
Settings → Actions → General → Access → "Accessible from repositories in the
devicecloud-dev organization".
