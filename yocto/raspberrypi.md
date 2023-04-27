# Raspberry Pi

## Create file structure

```bash
mkdir yocto
cd yocto
mkdir sources
```

## Layers

```bash
cd sources
git clone git://git.yoctoproject.org/poky -b kirkstone 
git clone --depth=1 git://git.yoctoproject.org/poky -b kirkstone 
git clone --depth=1 git://git.yoctoproject.org/meta-raspberrypi -b kirkstone
git clone --depth=1 https://git.openembedded.org/meta-openembedded -b kirkstone
git clone --depth=1 git://git.openembedded.org/meta-openembedded -b kirkstone
```

## Initialise environment

```bash
cd .. 
. sources/poky/oe-init-build-env
```

## Update bblayers.conf

```
# POKY_BBLAYERS_CONF_VERSION is increased each time build/conf/bblayers.conf
# changes incompatibly
POKY_BBLAYERS_CONF_VERSION = "2"

BBPATH = "${TOPDIR}"
BBFILES ?= ""

BBLAYERS ?= " \
  ${TOPDIR}/../sources/poky/meta \
  ${TOPDIR}/../sources/poky/meta-poky \
  ${TOPDIR}/../sources/poky/meta-yocto-bsp \
  ${TOPDIR}/../sources/meta-raspberrypi \
  ${TOPDIR}/../sources/meta-openembedded/meta-oe \
  ${TOPDIR}/../sources/meta-openembedded/meta-multimedia \
  ${TOPDIR}/../sources/meta-openembedded/meta-networking \
  ${TOPDIR}/../sources/meta-openembedded/meta-python \
  "
```

## Update local.conf

```
MACHINE ?= "raspberrypi4-64"
```

## Build

```bash
cd build
bitbake core-image-base
```

## Copy to flash drive

```bash
sudo bmaptool -d copy core-image-base-raspberrypi4.wic.bz2 /dev/sda
```
