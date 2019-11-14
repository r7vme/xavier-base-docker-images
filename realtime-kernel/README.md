# Rebuild L4T Linux kernel with PREEMPT_RT (real-time) patch

```
docker build -t xavier-rt-kernel:32.2.1 -f Dockerfile.l4t_32_2_1 .
docker run --rm -v $(pwd):/host xavier-rt-kernel:32.2.1 \
  cp /Linux_for_Tegra/kernel/kernel_supplements.tbz2 \
     /Linux_for_Tegra/bootloader/payloads_t19x/bl_update_payload \
     /Linux_for_Tegra/bootloader/payloads_t19x/bl_update_payload_default \
       /host
```

`bl_update_payload` - image for OTA update containing real-time kernel
`bl_update_payload_default` - image for OTA update containing default kernel
`kernel_supplements.tbz2` - archive containing kernel modules. Should be extracted to `/lib/modules`
