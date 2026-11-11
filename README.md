# Ramekin firmware for the NanoPi R76S

Official [Ramekin](https://github.com/ascension-association/ramekin) firmware for the [FriendlyElec NanoPi R76S](https://www.friendlyelec.com/index.php?route=product/product&product_id=308): gokrazy, the kernel ([kernel.r76s](https://github.com/ascension-association/kernel.r76s)), ramekind (the Ramekin device agent) and [gk-lockscreen](https://github.com/ascension-association/gk-lockscreen) for the HDMI screen. It contains no app and no project, so anyone can flash it and use it for their own project.

## Use it

1. Flash the disk image from the [latest release](https://github.com/ascension-association/firmware.r76s/releases) to a microSD card of 2 GB or more:
   ```sh
   curl -LO https://github.com/ascension-association/firmware.r76s/releases/latest/download/r76s.img.gz
   gunzip -c r76s.img.gz | sudo dd of=/dev/sdX bs=4M conv=fsync status=progress
   ```
2. Boot the device on your network. It starts **unclaimed** and shows a 6-digit pairing code on its HDMI screen ("Pairing code 239748"). Once it's claimed, the screen shows its IP address.
3. In your Ramekin project, point `ramekin.yaml` at this firmware:
   ```yaml
   firmware:
     github: ascension-association/firmware.r76s
     board: r76s
   ```
4. Claim the device. It takes your project's signing key, settings and fleet key, so it can install your current app release right away:
   ```sh
   ramekin device claim kitchen-01 <its-ip> --health-http http://127.0.0.1/
   ```

5. Optional: to run without the SD card, install onto the board's eMMC. The device keeps its identity and app, then powers off. Remove the card and power it on.
   ```sh
   ramekin device install kitchen-01 --to emmc --overwrite   # --overwrite erases FriendlyElec's preinstalled OS
   ```

The image is 1.25 GB. On first boot the device grows its storage (`/perm`) to fill the card, or the eMMC after an install.

To hand a device to another project, or start over, `ramekin device release <name>` factory-resets it: it keeps this firmware and shows a new pairing code.

Claimed devices follow this repository's `stable` channel (`r76s/stable.json` on `main`), and install new firmware with a test boot: they keep it only if they come up healthy. An update restarts the device two or three times (into the new image, then once more to confirm it, or back to the old one), so its app is briefly unavailable a few times.

## Trust

- **Firmware:** releases are signed with the key in [`keys/firmware-sign.pub`](keys/firmware-sign.pub), and devices refuse anything else. Releases aren't encrypted: they contain no secrets.
- **Apps:** they're signed with your project's own key, which the device learns when you claim it with the pairing code.
- **No gokrazy password:** the image has none, so gokrazy's web interface is reachable only from the device itself. The image is public, so a password in it would be public too.

## Maintainers

`firmware.yaml` describes the build. The gokrazy instance is `~/gokrazy/firmware.r76s`, and the signing key is `keys/firmware-sign.key`, which is never committed. Back it up: firmware can't be published for these devices without it.

```sh
ramekin firmware publish --version 1.0.1
```

The kernel lives on the device's single boot partition, so a broken kernel can't be rolled back. Test new kernels on a device before publishing them.
