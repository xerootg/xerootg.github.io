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
Server = https://github.com/xerootg/arch/releases/download/custom-repo
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

All three repos are served from GitHub Releases on
[xerootg/arch](https://github.com/xerootg/arch). They used to be split by size:
GitHub refuses any pushed file over 100 MB, so `ghidra-noprompt` and
`orca-slicer-git` could not live in this repo and needed their own. Release
assets allow 2 GB, so that constraint is gone.

**`https://xerootg.github.io/archlinux` no longer exists.** Packages were removed
from this repository entirely — deleting them in an ordinary commit would have
reclaimed nothing, since git keeps every blob it has ever seen, which is how this
repo once reached 8.39 GB. Release assets can actually be deleted, so superseded
packages are pruned on every build instead of accumulating.

If your `pacman.conf` still points at the old URL, `enroll.sh client` rewrites it
in place.

## What is in this repo

| path | what it is |
|---|---|
| `xerootg.asc` | the public signing key, exported from the key that actually signs |
| `index.html` | the generated package listing |
| `data/packages.csv` | current contents of every repo, one row per package |
| `data/changes.csv` | append-only log of adds, updates and removals |
| `data/state.json` | last check time and a digest used to skip empty commits |

Everything except this file is generated. It is written by
[`build-repo-index.py`](https://github.com/xerootg/arch/blob/main/.github/scripts/build-repo-index.py),
which reads each repo's pacman database and rewrites the page from it. Do not
hand-edit them — the next run overwrites the lot.

`.nojekyll` keeps GitHub Pages serving these files statically, with no build step
in the way. The whole repository is now about 46 KB of text — packages live in
releases, so it has no way to grow the way it did before.
