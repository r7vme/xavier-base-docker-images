# Jetpack for NVIDIA Jetson AGX Xavier in docker

Image includes:
- nvidia drivers
- cuda
- cuDNN
- opencv
- TensorRT

Prerequisites:
- Flash your Xavier with Jetpack without any packages.
- Instead download deb packages with `sdkmanager` and copy to root of this repo (See below).

To build:
```
docker build -t jetpack42 -f Dockerfile.jetpack42 .
# squashed (single layer) 1.8GB smaller. 5.21GB vs 6.93GB
# docker build --squash -t jetpack42 -f Dockerfile.jetpack42 .
```

To run:
```
docker run \
  -ti \
  --rm \
  --device /dev/nvhost-as-gpu \
  --device /dev/nvhost-ctrl \
  --device /dev/nvhost-ctrl-gpu \
  --device /dev/nvhost-ctxsw-gpu \
  --device /dev/nvhost-dbg-gpu \
  --device /dev/nvhost-gpu \
  --device /dev/nvhost-prof-gpu \
  --device /dev/nvhost-sched-gpu \
  --device /dev/nvhost-tsg-gpu \
  --device /dev/nvmap \
  --name jetpack42 \
  jetpack42 bash
```

# How to get cuda, cuDNN and other packages required for these docker images?

Unfortunately for Jetpack 4.2 repos require authentication (developer.nvidia.com account),
so in order to build docker images with cuda we need to predownload them.

The easiest way to do it with sdkmanager. If you alsready have sdkmanager running, you probably already have these
packages under download directory (e.g. `~/Downloads/nvidia/sdkm_downloads`).

If not either follow official instructions or use "sdkmanager on any linux" section.

# sdkmanager on any linux

By default Jetpack can be installed only from Ubuntu 16.04 or 18.04. Fortunately there are multiple ways to avoid it. In my case i have Arch linux and here is what worked for me.

Download sdkmanager deb package from [downloads center](https://developer.nvidia.com/embedded/downloads).

Then unpack and run sdkmanager
```
ar x sdkmanager_0.9.11-3405_amd64.deb
tar xf data.tar.xz
./opt/nvidia/sdkmanager/sdkmanager
```

There can be some dependencies missing, but it has no fancy dependecies, so should work on most desktop linux (e.g. in my case only `lbzip2` was missing).

# flashing w/o sdkmanager

It's possible to flash xavier completely w/o `sdkmanager`.

```
wget https://developer.nvidia.com/embedded/dlc/l4t-jetson-driver-package-32-1-JAX-TX2
wget https://developer.nvidia.com/embedded/dlc/l4t-sample-root-filesystem-32-1-JAX-TX2
sudo tar xpf JAX-TX2-Jetson_Linux_R32.1.0_aarch64.tbz2
cd Linux_for_Tegra/rootfs/
sudo tar xpf ../../JAX-TX2-Tegra_Linux_Sample-Root-Filesystem_R32.1.0_aarch64.tbz2
cd ..
sudo ./apply_binaries.sh
# 1. connect your xavier to USB
# 2. press and hold RECOVERY button and then press POWER button
# 3. release both buttons
# 4. lsusb should see NVIDIA device
sudo ./flash.sh jetson-xavier mmcblk0p1
```
