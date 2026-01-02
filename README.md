# Raspberry Pi 5 Talos Builder

Forked from [talos-rpi5/talos-builder](https://github.com/talos-rpi5/talos-builder) and modified as follows.

- Add changes in [PR #20](https://github.com/talos-rpi5/talos-builder/pull/20/commits/f2f351025e9d0a0409899b0351a44e3231a44280), specifically for multiple extensions
- Add iscsi-tools extension
- Modify talos Makefile to accomodate local build environment

The motiviation to do this is to build a Talos RPi 5 cluster with NVMe drives for use with [Longhorn](https://longhorn.io).

## Build environment

- MacOS 26 on Apple M2 Pro
- crane 0.20.7
- Docker 28.3.2
- git 2.50.1
- gmake 4.4.1

## Pre-build tasks

```sh
# Log into GitHub
docker login ghcr.io

# set GitHub token (for crane push)
export GITHUB_TOKEN=<secret>
# Clone all dependencies and apply patches
gmake checkouts patches

# Modify talos Makefile due to [known issue](https://github.com/docker/buildx/issues/2733)
gsed -i 's/rewrite-timestamp=true/rewrite-timestamp=false/' checkouts/talos/Makefile

# Replace `sed` with `gsed` in talos Makefile
gsed -i 's/\ sed\ /\ gsed\ /g' checkouts/talos/Makefile

# commit changes locally so the build is not flagged as dirty
git commit -am "fixes for local build env"
```

## Building

Modified build notes

```sh
# Builds the Linux Kernel (can take a while)
gmake REGISTRY=ghcr.io REGISTRY_USERNAME=apfuzz kernel

# Builds the overlay (U-Boot, dtoverlays ...)
gmake REGISTRY=ghcr.io REGISTRY_USERNAME=apfuzz overlay

# Change visibility of sbc-raspberrypi5 package to public

# Final step to build the installer and disk image
gmake REGISTRY=ghcr.io REGISTRY_USERNAME=apfuzz installer

# Change visibility of installer package to public
```
