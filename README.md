# signing

Every Briard release manifest, and the job that signs it.

A release of [briardhq/briard](https://github.com/briardhq/briard) is a set of manifests, one per
chain and platform, each naming its artifacts by size and SHA-256. A node runs an artifact only if a
manifest naming it carries an Ed25519 signature from a key in the node's keyring. This repository is
where that signature is made.

- `releases/<id>/<platform>/manifest.json` is the manifest, byte for byte as it is served, and
  `manifest.json.sig` beside it is its raw 64-byte detached signature.
- `.github/workflows/sign.yml` holds the key, as a secret of an environment that only `main` can
  use. A push to `main` that touches `releases/` signs every manifest that has no signature and
  commits the signatures.
- Only the owner can write here. A release arrives as a pull request from the publisher's fork,
  and merging it is the act of signing.

## Reviewing a release pull request

The description is written by the machine that opened it, so read it as a claim. Merge only if:

- every changed file is under `releases/<id>/`, for one release id, and no file is outside
  `releases/`;
- the id is one you just staged, and its commit (the id's last part) is on briard's `main`;
- its platforms are the expected ones: `linux-amd64` and `windows-amd64` for a briard id, `amd64`
  for a vm id;
- nothing about it is unusual: the time, the number of manifests, a second open pull request.

A file deleted beside a manifest is a `.sig` dropped because the same id was staged again before it
was published.

## The catalog

`catalog/` is the published app catalog, byte for byte as nodes read it from
`https://get.briard.io/catalog/`: `catalog/<app>/<version>.json`, one file per version of an app,
kept for good, and `catalog/latest.json`, the list naming each app's current version file by the
SHA-256 of its bytes. Only the list is signed, by the same job and key, and it is signed again
whenever its signature does not verify — after a change, and after a key rotation. Before it signs,
the job checks every line's version file against its hash and, for every line that is new, that
the version did not go down, that the images come from the repositories the app's previous version
used, and that each is what an upstream tag of that version resolves to at that moment; it refuses
otherwise, and signs nothing. The catalog's source is
[briardhq/catalog](https://github.com/briardhq/catalog); what is merged here is what was tested
there and is about to be published.

## Reviewing a catalog pull request

The description lists every candidate with its verdicts and is, again, a claim. Merge only if:

- every changed file is under `catalog/`: new version files, and lines of `catalog/latest.json`;
- no existing version file changes, and no `.sig` is added, changed or deleted;
- each changed line's version goes up, and names a version file the pull request adds;
- an app new to the list is one you meant to add, and its image repository is its project's own.

A pull request that also touches `releases/` or `.github/` is not a catalog pull request. The job
re-checks the hashes, the versions, the repositories and the upstream tags itself, and a refusal is
a red run on `main` with nothing signed.
