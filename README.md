# Raspberry Pi 5 Talos Builder

Forked from [talos-rpi5/talos-builder](https://github.com/talos-rpi5/talos-builder) and modified as follows.

- Add changes in [PR #20](https://github.com/talos-rpi5/talos-builder/pull/20/commits/f2f351025e9d0a0409899b0351a44e3231a44280), specifically for multiple extensions
- Add iscsi-tools extension
- Modify checkouts/talos/Makefile to add `unpack=false` to registry build target due to [known issue](https://github.com/docker/buildx/issues/2733)
- replace `sed` with `gsed` where applicable

The motiviation to do this is to build a Talos RPi 5 cluster with NVMe drives for use with [Longhorn](https://longhorn.io).

## Build environment

- MacOS 26 on Apple M2
- Docker 28.3.2
- git 2.50.1
- gmake 4.4.1

## Building

Modified build notes

```sh
# Clones all dependencies and applies the necessary patches
make checkouts patches

# Modify checkouts/talos/Makefile to add `unpack=false` to registry build target due to [known issue](https://github.com/docker/buildx/
gsed -i 's/type=image/type=image,unpack=false/' checkouts/talos/Makefile 

# Builds the Linux Kernel (can take a while)
make REGISTRY=ghcr.io REGISTRY_USERNAME=apfuzz kernel

# Builds the overlay (U-Boot, dtoverlays ...)
make REGISTRY=ghcr.io REGISTRY_USERNAME=apfuzz overlay

# Final step to build the installer and disk image
make REGISTRY=ghcr.io REGISTRY_USERNAME=apfuzz installer
```
