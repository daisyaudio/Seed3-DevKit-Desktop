# Seed3 Desktop Dev Kit

<img width="100%" height="auto" alt="Desktop-Dev-Kit-transparent" src="https://github.com/user-attachments/assets/e8f8e04b-7dbd-40b8-a2a7-ea85a9638a67" />


## A Synth, Sampler, and Effects Platform for the Daisy Seed3

The Seed3 Desktop Dev Kit is the ultimate Daisy-powered prototyping platform for synths, samplers, and effects. Built around the Seed3, it gives you everything you need to design powerful polysynths, sequenced samplers, and rich DSP effects — all on the same hardware.

With essential audio, MIDI, CV, and control components at your fingertips, the Desktop Dev Kit makes it easy to bring your ideas to life. From stereo audio I/O to hands-on knobs and switches, it's packed with the tools you need to shape your next instrument or effect. Add USB-C connectivity, a microSD card slot, and a dedicated headphone amp, and you've got a ready-made studio workbench for turning concepts into commercial products.

---

## Contents

- [Features](#features)
- [Specifications](#specifications)
- [Getting Started](#getting-started)
- [Hardware Reference](#hardware-reference)
- [Resources & Support](#resources--support)
- [Open-Source Hardware](#open-source-hardware)
- [License](#license)

---

## Features

| Category | Details |
| --- | --- |
| **Audio** | Stereo line-level input and output, with input jack detection |
| **Headphones** | 3.5 mm headphone output with volume control |
| **MIDI** | DIN MIDI In, Out, and Thru |
| **CV & Gate** | 2 × CV inputs, 2 × gate inputs, 2 × CV outputs |
| **USB** | USB-C port |
| **Storage** | microSD slot (with card detect) for firmware updates, samples, presets, configuration, and more |
| **Potentiometers** | 8 × 10 kΩ linear (B-taper) |
| **Buttons** | 16 × tactile switches, each with an LED |
| **Toggle Switches** | 2 × toggle switches (1 × ON-OFF-ON, 1 × ON-ON) |

## Specifications

<!-- TODO: Fill in or remove any rows that don't apply. -->

| Parameter | Value |
| --- | --- |
| Processor module | Daisy Seed3 |
| Power | +5V USB-C |
| Current draw | Firmware dependent |
| Audio codec / sample rate | TAC5242 / up to 32-bit, 192kHz |
| Input impedance | 20KΩ |
| Output impedance | 100Ω |
| CV input range | -5V to +5V |
| CV output range | 0V to +5V |
| Gate input threshold | +0V4 |
| Board dimensions | 197 mm × 111 mm |
| MIDI connectors | 3 × 5-pin DIN (In, Out, Thru) |

## Getting Started

### 1. Set up the toolchain

Install the Daisy toolchain and clone the libraries by following the setup guide at [docs.daisy.audio](https://docs.daisy.audio).

- [libDaisy](https://github.com/electro-smith/libDaisy) — hardware abstraction library
- [DaisySP](https://github.com/electro-smith/DaisySP) — DSP library

### 2. Build the template
 
A ready-to-go starting project for the Desktop Dev Kit lives in libDaisy at
[`examples/devkits/Desktop-DevKit-UI-Template`](https://github.com/daisyaudio/libDaisy/tree/master/examples/devkits/Desktop-DevKit-UI-Template).
 
```bash
git clone --recurse-submodules https://github.com/daisyaudio/libDaisy
cd libDaisy
make
cd examples/devkits/Desktop-DevKit-UI-Template
make
```
 
Copy the template folder to start your own instrument, then edit the audio callback. Inputs are `in[0][i]` (left) and `in[1][i]` (right), and outputs are `out[0][i]` and `out[1][i]` (see [Audio](#audio)).

### 3. Flash the Seed3

Connect the Dev Kit to your computer over USB-C, put the Seed3 into bootloader mode, then flash:

```bash
make program-dfu
```

### 4. Power up

<!-- TODO: describe the power connection. -->

Connect power, then plug in your audio, MIDI, and CV connections.

## Hardware Reference

### Pinout

<img width="100%" height="auto" alt="Seed3 Desktop Dev Kit pinout" src="https://daisy.nyc3.cdn.digitaloceanspaces.com/products/seed-3-desktop/seed3-desktop-dev-kit-pinout-dark.png" />

A printable [Pinout PDF](https://daisy.nyc3.cdn.digitaloceanspaces.com/products/seed-3-desktop/Seed3-Desktop-Dev-Kit-Pinout.pdf) is also available.

### Audio

| Jack | Signal | Audio Callback |
| --- | --- | --- |
| Input Left | `AUDIO_IN_L` | `in[0][i]` |
| Input Right | `AUDIO_IN_R` | `in[1][i]` |
| Output Left | `AUDIO_OUT_L` | `out[0][i]` |
| Output Right | `AUDIO_OUT_R` | `out[1][i]` |
| Headphone Left | `AUDIO_OUT_L` | `out[0][i]` (mirrors Output Left) |
| Headphone Right | `AUDIO_OUT_R` | `out[1][i]` (mirrors Output Right) |

#### Input Detection

| Signal | Seed3 Pin |
| --- | --- |
| `LEFT_DETECT` | D16 |
| `RIGHT_DETECT` | D17 |

### Potentiometers (8-channel multiplexer)

The eight pots are read through a single ADC pin via an 8-channel analog multiplexer. Set the select lines A/B/C to choose a channel, then read `POT_MUX`.

| Signal | Seed3 Pin |
| --- | --- |
| `POT_MUX` (ADC) | D15 |
| `MUX_CTRL_A` | D24 |
| `MUX_CTRL_B` | D25 |
| `MUX_CTRL_C` | D26 |

| Pot | Mux Channel |
| --- | --- |
| VR1 | `MUX_CH_0` |
| VR2 | `MUX_CH_1` |
| VR3 | `MUX_CH_2` |
| VR4 | `MUX_CH_3` |
| VR5 | `MUX_CH_4` |
| VR6 | `MUX_CH_5` |
| VR7 | `MUX_CH_6` |
| VR8 | `MUX_CH_7` |

### Tactile Switches (2 × shift register)

The 16 tactile switches are read through two daisy-chained shift registers.

| Signal | Seed3 Pin |
| --- | --- |
| `SR_LATCH` | D9 |
| `SR_CLK` | D8 |
| `SR_DATA` | D10 |

Switches `S1`–`S16` map to `TAC_SW1`–`TAC_SW16`, arranged in a 4 × 4 grid:

| | | | |
| --- | --- | --- | --- |
| S1 | S2 | S3 | S4 |
| S5 | S6 | S7 | S8 |
| S9 | S10 | S11 | S12 |
| S13 | S14 | S15 | S16 |

### LEDs (I²C LED driver)

The 16 LEDs (`LED_1`–`LED_16`, board refs D1–D16) sit in the same 4 × 4 grid as the tactile switches and are driven by an LED driver over I²C.

| Signal | Seed3 Pin |
| --- | --- |
| `I2C_SCL` | D11 |
| `I2C_SDA` | D12 |

> [!NOTE]
> LED board refs D1–D16 are component designators, not Seed3 pins.

### Toggle Switches

| Switch | Signal | Seed3 Pin | Type |
| --- | --- | --- | --- |
| Toggle Switch 17 | `TOG_1` | D19 | ON-ON |
| Toggle Switch 18 | `TOG_2_A` / `TOG_2_B` | D28 / D27 | ON-OFF-ON (3-position, two pins) |

### CV & Gates

| Jack | Signal | Seed3 Pin |
| --- | --- | --- |
| Gate In 1 | `GATE_IN1` | D0 |
| Gate In 2 | `GATE_IN2` | D18 |
| CV In 1 | `CV_IN_1` | D20 |
| CV In 2 | `CV_IN_2` | D21 |
| CV Out 1 | `CV_OUT_1` | D22 |
| CV Out 2 | `CV_OUT_2` | D23 |

### MIDI

| Jack | Signal | Seed3 Pin | Direction |
| --- | --- | --- | --- |
| MIDI Out | `MIDI_TX` | D13 | Out |
| MIDI In | `MIDI_RX` | D14 | In |
| MIDI Thru | `MIDI_RX` | D14 | Mirrors MIDI In |

### microSD (SDMMC, 4-bit)

| Signal | Seed3 Pin |
| --- | --- |
| `SDMMC_CK` | D6 |
| `SDMMC_CMD` | D5 |
| `SDMMC_D0` | D4 |
| `SDMMC_D1` | D3 |
| `SDMMC_D2` | D2 |
| `SDMMC_D3` | D1 |
| `SDMMC_DETECT` | D7 |

### USB-C

| Signal | Seed3 Pin |
| --- | --- |
| `USB_OTG_HS_N` | D29 |
| `USB_OTG_HS_P` | D30 |

## Resources & Support

- **Product Page:** [Seed3 Desktop Dev Kit](https://daisy.audio/products/seed-3-desktop-dev-kit)
- **Documentation:** [docs.daisy.audio](https://docs.daisy.audio/product/Seed3-Desktop-Dev-Kit/)
- **Community Forum:** [community.daisy.audio](https://community.daisy.audio)
- **Discord:** [Daisy Discord](https://discord.gg/ByHBnMtQTR)
- **Issues:** Report bugs or hardware errata via this repository's [Issues](../../issues) tab.

### Design Files

- [Schematic (PDF)](https://daisy.nyc3.cdn.digitaloceanspaces.com/products/seed-3-desktop/ES-Seed3-DevKit-Desktop-Rev3.pdf)
- [Bill of Materials (CSV)](https://daisy.nyc3.cdn.digitaloceanspaces.com/products/seed-3-desktop/Seed3-DevKit-Desktop_Rev4-bom.csv)
- [Pinout (PDF)](https://daisy.nyc3.cdn.digitaloceanspaces.com/products/seed-3-desktop/Seed3-Desktop-Dev-Kit-Pinout.pdf)
- [KiCad Design Files](https://github.com/electro-smith/Seed3-DevKit-Desktop/releases/latest)

---

## Open-Source Hardware

<img width="256px" height="auto" alt="Open Source Hardware logo" src="https://github.com/user-attachments/assets/f9264744-3509-4cf0-9f4a-981cb05eb38e" />

The Desktop Dev Kit is open-source hardware and carries the essential circuitry needed to use Daisy in synths, samplers, and desktop effects. Use the BOM, schematics, and board files to kickstart your own desktop instrument designs.

## License

The Seed3 Desktop Dev Kit is released under the permissive **MIT License**. This means that:

- Designers are free to use, study, modify, share, and distribute the hardware designs and products based on them.
- Designers may use any and all provided designs in closed-source commercial products.

See [LICENSE](LICENSE) for the full text.

© 2026 Qu-Bit Electronix, Inc. (dba Daisy)
