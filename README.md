# Ramekin firmware for the NanoPi R76S

Official [Ramekin](https://github.com/ascension-association/ramekin) firmware for the FriendlyElec NanoPi R76S (RK3576): gokrazy, the kernel ([kernel.r76s](https://github.com/ascension-association/kernel.r76s)) and ramekind, the Ramekin device agent. It contains no app and no project, so anyone can flash it and use it for their own project.

## Use it

1. Flash the boot and root partition images from the [latest release](https://github.com/ascension-association/firmware.r76s/releases), or build the image yourself with `gok`.
2. Boot the device on your network. It starts **unclaimed** and shows a 6-digit pairing code on its console.
3. In your Ramekin project, point `ramekin.yaml` at this firmware:
   ```yaml
   firmware:
     github: ascension-association/firmware.r76s
     board: r76s
   ```
4. Claim the device. It takes your project's signing key and settings, and from then on installs your app releases:
   ```sh
   ramekin device claim kitchen-01 <its-ip> --health-http http://127.0.0.1/
   ```

Claimed devices follow this repository's `stable` channel (`r76s/stable.json` on `main`), and install new firmware with a test boot: they keep it only if they come up healthy.

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
