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
