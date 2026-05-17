# VDXP — Visual Data eXchange Protocol

**A unidirectional data transfer protocol over HDMI for air-gapped environments.**

VDXP encodes arbitrary binary data as visual cell patterns displayed on screen. The signal is captured via HDMI output and a USB capture device, then decoded to reconstruct the original data. No network connection. No USB drives.

![VDXP Overview](vdxp.png)

## Overview

```
[Sender] → Cell pattern on screen → HDMI → [USB Capture] → Decode → [Receiver]
```

### How It Works

1. **Sender** splits a file into chunks, applies Reed-Solomon error correction, and renders each chunk as a grid of colored (or grayscale) cells on the HDMI output.
2. **Capture device** digitizes the HDMI signal via USB.
3. **Receiver** decodes cell values, corrects errors via RS, and reassembles the original file.

### Key Design Elements

- **Cell grid encoding** — The display is divided into a uniform grid of cells. Each cell carries multiple bits of data encoded as a color or grayscale level.
- **Color palettes** — BGR 8-color (cube vertices) for lossy-compressed capture; Y-only grayscale (Y16/Y32/Y64) for uncompressed capture.
- **Reed-Solomon error correction** — Per-block FEC over GF(2^8) enables reliable transfer even with capture artifacts.
- **3-stage decode filter** — Fingerprint check → Header-only decode → Full decode. Minimizes CPU load by rejecting duplicates early.
- **Loop playback** — Sender displays frames in a continuous loop; receiver collects until all unique frames are received.

## Key Properties

- **Unidirectional** — Data flows one way only (display → capture). No return channel.
- **Air-gap safe** — Works across physically isolated networks using only HDMI + USB capture.
- **Hardware agnostic** — Works with any HDMI output and USB capture device.
- **Integrity** — Reed-Solomon provides per-block error detection/correction. Application-layer hash verification (e.g., SHA-256) is recommended for end-to-end validation.

## Performance Range

| Configuration | Throughput | Hardware |
|---------------|-----------|----------|
| Basic (MJPEG, 8-color) | ~100 KB/s | USB2.0 MJPEG capture (~¥15,000) |
| Standard (YUY2, Y16) | ~1,000 KB/s | USB3.0 YUY2 device |
| Accelerated (YUY2, Y32) | ~15,000 KB/s | USB3.0 YUY2 + accelerated decoder |

## Status

VDXP is currently implemented in [vdxpy](https://github.com/blackocean-tech/vdxpy). The encoding format is defined by the reference implementation and may evolve based on real-world usage. A formal protocol specification will be published when interoperability with independent implementations becomes a priority.

## Implementations

| Name | Language | License | Description |
|------|----------|---------|-------------|
| [vdxpy](https://github.com/blackocean-tech/vdxpy) | Python | MIT | Reference implementation |

## License

This document is released under the [CC-BY-4.0](LICENSE) license.

## Contact

- **Black Ocean Technologies** — [blackocean.tech](https://blackocean.tech/)
- Enterprise / government inquiries: **contact@blackocean.tech**
