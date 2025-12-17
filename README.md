# Xero's package feed

Offers prebuilds of packages I use or have authored

## Arch linux
 - [orca-slicer-git](https://aur.archlinux.org/packages/orca-slicer-git)
 - [libfprint-elanmoc2-slg3-git](https://github.com/xerootg/libfprint-elanmoc2-slg3-git) which builds [libfprint](https://github.com/xerootg/libfprint) for surface laptop go fingerprint readers
 - [imhex](https://aur.archlinux.org/packages/imhex)

## adding to your machine
edit /etc/pacman.conf with the tool of your choice, adding this to the end somewhere
```
[custom]
Server = https://xerootg.github.io/archlinux
SigLevel = Optional TrustAll
```