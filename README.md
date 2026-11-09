# signing

The signatures behind [Briard](https://github.com/briardhq/briard)'s releases and app catalog.

- `releases/<id>/<platform>/manifest.json` + `.sig`: a release manifest and its Ed25519 signature.
- `catalog/<app>/<version>.json`: one version of an app. `catalog/latest.json` + `.sig`: the signed
  list a node reads, naming each version file by its SHA-256.
- `.github/workflows/sign.yml` holds the release key and signs what the owner merges.

Only the owner can write here; changes arrive as pull requests from a fork, and merging one is the
act of signing. The description is the opener's claim: read the files.

## Reviewing

A **release**: every file under `releases/<id>/` for one id you just staged, whose commit is on
briard's `main`, with the expected platforms (`linux-amd64` + `windows-amd64`, or `amd64` for a VM).

A **catalog** change: only new version files under `catalog/` and their lines in
`catalog/latest.json`; no `.sig` touched; each version goes up; a new app is one you meant to add.

Anything touching `.github/` is a change to the job itself and is reviewed as one.
