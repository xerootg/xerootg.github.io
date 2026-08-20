# Xero's package feed

Prebuilt Arch Linux packages I use or maintain.

**➜ [xerootg.github.io](https://xerootg.github.io/)** — the live package list, with
versions, sizes, build dates and a changelog.

## Adding the repos

Packages are signed, so the snippet includes a key import:

```bash
curl -fsSL https://xerootg.github.io/xerootg.asc | sudo pacman-key --add -
sudo pacman-key --lsign-key "$(curl -fsSL https://xerootg.github.io/xerootg.asc \
  | gpg --show-keys --with-colons | awk -F: '/^fpr:/{print $10; exit}')"

sudo tee -a /etc/pacman.conf > /dev/null <<'EOF'

[custom]
Server = https://xerootg.github.io/archlinux
SigLevel = Required DatabaseOptional

[ghidra]
Server = https://github.com/xerootg/arch/releases/download/pacman-repo
SigLevel = Required DatabaseOptional

[orca]
Server = https://github.com/xerootg/arch/releases/download/orca-repo
SigLevel = Required DatabaseOptional
EOF
sudo pacman -Sy
```

**[xerootg.github.io](https://xerootg.github.io/) carries the authoritative
version of this snippet**, generated with the current fingerprint filled in and
with each repo's `SigLevel` set from whether its database signature actually
verifies — so it can never tell you to enable a setting the repo would then
fail. Prefer it over the copy above.

`--lsign-key` is the step people miss: `--add` imports the key, but pacman keeps
its own keyring and still refuses every package until the key is locally signed
as trusted.

`[custom]` is served from this repo. `[ghidra]` and `[orca]` hold single packages
too large to push here (GitHub refuses any pushed file over 100 MB), so they are
served from releases on [xerootg/arch](https://github.com/xerootg/arch) instead.

## What is in this repo

| path | what it is |
|---|---|
| `archlinux/` | the `[custom]` pacman repo: packages, `custom.db`, and a `.sig` beside each |
| `xerootg.asc` | the public signing key, exported from the key that actually signs |
| `index.html` | the generated package listing |
| `data/packages.csv` | current contents of every repo, one row per package |
| `data/changes.csv` | append-only log of adds, updates and removals |
| `data/state.json` | last check time and a digest used to skip empty commits |

Everything except this file is generated. `archlinux/` is written by
`build-pacman-repo`; the rest by
[`build-repo-index.py`](https://github.com/xerootg/arch/blob/main/.github/scripts/build-repo-index.py),
which reads each repo's pacman database and rewrites the page from it. Do not
hand-edit them — the next run overwrites the lot.

`.nojekyll` keeps GitHub Pages serving these files statically. There is no build
step between publishing a package and it being downloadable, which matters when
the site is mostly a few hundred megabytes of `.pkg.tar.zst`.
