# Seed3 Desktop Dev Kit

<img width="100%" height="auto" alt="Seed3 Desktop Dev Kit" src="https://github.com/user-attachments/assets/e8f8e04b-7dbd-40b8-a2a7-ea85a9638a67" />

## A Synth, Sampler, and Effects Platform for the Daisy Seed3

The Seed3 Desktop Dev Kit is the ultimate Daisy-powered prototyping platform for synths, samplers, and effects. Built around the Seed3, it gives you everything you need to design powerful polysynths, sequenced samplers, and rich DSP effects — all on the same hardware.

With essential audio, MIDI, CV, and control components at your fingertips, the Desktop Dev Kit makes it easy to bring your ideas to life. From stereo audio I/O to hands-on knobs and switches, it's packed with the tools you need to shape your next instrument or effect. Add USB-C connectivity, a microSD card slot, and a dedicated headphone amp, and you've got a ready-made studio workbench for turning concepts into commercial products.

---

## Contents

- [Features](#features)
- [Specifications](#specifications)
- [Getting Started](#getting-started)
- [Making Your Own Project](#making-your-own-project)
- [Hardware Reference](#hardware-reference)
- [Resources & Support](#resources--support)
- [Open-Source Hardware](#open-source-hardware)
- [License](#license)

---

## Features

| Category | Details |
| --- | --- |
| **Audio** | Stereo line-level input and output, with input jack detection |
| **Headphones** | 3.5mm headphone output with volume control |
| **MIDI** | DIN MIDI In, Out, and Thru |
| **CV & Gate** | 2 × CV inputs, 2 × gate inputs, 2 × CV outputs |
| **USB** | USB-C port for power, programming (with the Daisy bootloader), and USB serial |
| **Storage** | microSD slot (with card detect) for firmware updates, samples, presets, configuration, and more |
| **Potentiometers** | 8 × 10kΩ linear (B-taper) |
| **Buttons** | 16 × tactile switches, each with an LED |
| **Toggle Switches** | 2 × toggle switches (1 × ON-OFF-ON, 1 × ON-ON) |

## Specifications

| Parameter | Value |
| --- | --- |
| Processor module | Daisy Seed3 |
| Power | +5V via the Dev Kit's USB-C port |
| Current draw | Firmware dependent |
| Audio codec / sample rate | TAC5242 / up to 32-bit, 192kHz |
| Input impedance | 20kΩ |
| Output impedance | 100Ω |
| CV input range | -5V to +5V |
| CV output range | 0V to +5V |
| Gate input threshold | +0.4V |
| Board dimensions | 197mm × 111mm |
| MIDI connectors | 3 × 5-pin DIN (In, Out, Thru) |

## Getting Started

> [!IMPORTANT]
> This kit has two USB-C ports: one on the **Dev Kit board** (J7, above the Seed3) and one on the **Seed3 module** itself. The steps below say which one to use. Day-to-day, you power, program, and monitor the Dev Kit through the board's USB-C port.

### 1. Install the toolchain

Follow the Daisy [C++ Getting Started guide](https://docs.daisy.audio/tutorials/cpp-dev-env/). It installs the toolchain and clones [DaisyExamples](https://github.com/daisyaudio/DaisyExamples), which includes [libDaisy](https://github.com/daisyaudio/libDaisy) (hardware library) and [DaisySP](https://github.com/daisyaudio/DaisySP) (DSP library).

### 2. Update libDaisy

The Desktop Dev Kit template and board support are newer than the copy of libDaisy that DaisyExamples includes. From your `DaisyExamples` folder, update libDaisy to the latest version and rebuild it:

```bash
git submodule update --remote libDaisy
cd libDaisy
make
```

### 3. Build the template

The starting project for this kit is [`examples/devkits/Desktop-DevKit-UI-Template`](https://github.com/daisyaudio/libDaisy/tree/master/examples/devkits/Desktop-DevKit-UI-Template). It reads every input on the Dev Kit, manages the 16 buttons, 8 pots, and LEDs with libDaisy's UI class, and prints input changes over USB serial. From the `libDaisy` folder:

```bash
cd examples/devkits/Desktop-DevKit-UI-Template
make
```

This creates `Desktop-DevKit-UI-Template.bin` in the template's `build/` folder.

### 4. Install the Daisy bootloader (one time)

The template runs from the Daisy bootloader, using the variant that accepts new programs over the **Dev Kit's** USB-C port. You install the bootloader once, through the **Seed3's** USB-C port:

1. Connect a USB-C cable from your computer to the USB-C port on the Seed3 module.
2. Hold **BOOT**, press and release **RESET**, then release **BOOT**.
3. From the template folder, run:

   ```bash
   make program-boot
   ```

   Or choose the **v6.x (external)** bootloader in the [Daisy Web Programmer](https://flash.daisy.audio/).

You don't need to reinstall the bootloader unless you switch bootloader variants, or want to program an app to the Seed3's internal flash.

### 5. Flash the template

1. Move the USB-C cable to the **Dev Kit's** USB-C port (J7, above the Seed3). This port also powers the board.
2. Press **RESET**, then **BOOT** on the Seed3. The Seed3's USR LED blinks quickly, then pulses slowly, which means it's ready to receive a program.
3. From the template folder, run:

   ```bash
   make program-dfu
   ```

   Or, in the [Daisy Web Programmer](https://flash.daisy.audio/), upload the `Desktop-DevKit-UI-Template.bin` file from the `build/` folder.

### 6. Try it out

Once flashed, the Seed3's USR LED blinks at a steady rate, and:

- Pressing a tactile switch lights its LED.
- CV Out 1 and CV Out 2 output a slow ramp and a saw wave.
- MIDI notes sent to MIDI In are echoed to MIDI Out.
- Audio at the inputs passes straight through to the outputs and the headphone jack.
- A serial monitor connected to the Dev Kit's USB-C port shows a message whenever any input changes.

## Making Your Own Project

Edits only take effect on the Dev Kit after you rebuild and reflash, so start your own project from a copy of the template.

1. Copy the `Desktop-DevKit-UI-Template` folder into your `DaisyExamples` folder and rename it, for example `DaisyExamples/MyInstrument`.
2. In the copied `Makefile`:
   - Set `TARGET` to your project name, for example `TARGET = MyInstrument`.
   - Change `LIBDAISY_DIR` to point to libDaisy from the new location: `LIBDAISY_DIR = ../libDaisy`.
   - To use DaisySP, uncomment the DaisySP line and set `DAISYSP_DIR = ../DaisySP`.
   - Keep the `APP_TYPE=BOOT_SRAM` and `BOOT_BIN` lines. They make your program run from the bootloader you installed in step 4.
3. Write your instrument in `src/main.cpp`, and adjust the button and pot handling in `src/main_page.h`. Audio is processed in `AudioCallback()`: inputs are `in[0][i]` (left) and `in[1][i]` (right), and outputs are `out[0][i]` and `out[1][i]` (see [Audio](#audio)).
4. Rebuild and flash after every change, using the Dev Kit's USB-C port:

   ```bash
   make
   make program-dfu
   ```

   Press **RESET**, then **BOOT**, before each `make program-dfu`.

> [!TIP]
> The template builds with debugging enabled (`DEBUG = 1`, `OPT = -Og`). For release builds, set `DEBUG = 0` and `OPT = -O3` in the Makefile.

## Hardware Reference

The **Ref** column lists each part's reference designator, as printed on the board's silkscreen.

### Pinout

<img width="100%" height="auto" alt="Seed3 Desktop Dev Kit pinout" src="https://daisy.nyc3.cdn.digitaloceanspaces.com/products/seed-3-desktop/seed3-desktop-dev-kit-pinout-dark.png" />

A printable [Pinout PDF](https://daisy.nyc3.cdn.digitaloceanspaces.com/products/seed-3-desktop/Seed3-Desktop-Dev-Kit-Pinout.pdf) is also available.

### Audio

| Jack | Ref | Signal | Audio Callback |
| --- | --- | --- | --- |
| Input Left | J1 | `AUDIO_IN_L` | `in[0][i]` |
| Input Right | J2 | `AUDIO_IN_R` | `in[1][i]` |
| Output Left | J3 | `AUDIO_OUT_L` | `out[0][i]` |
| Output Right | J4 | `AUDIO_OUT_R` | `out[1][i]` |
| Headphones | J5 | `AUDIO_OUT_L` / `AUDIO_OUT_R` | `out[0][i]` / `out[1][i]` (mirrors the main outputs) |
| Headphone volume | RV1 | — | Analog volume control, not read by the Seed3 |

#### Input Detection

| Jack | Ref | Signal | Seed3 Pin |
| --- | --- | --- | --- |
| Input Left | J1 | `LEFT_DETECT` | D16 |
| Input Right | J2 | `RIGHT_DETECT` | D17 |

### Potentiometers (8-channel multiplexer)

The eight pots are read through a single ADC pin via an 8-channel analog multiplexer (U12). Set the select lines A/B/C to choose a channel, then read `POT_MUX`.

| Signal | Seed3 Pin |
| --- | --- |
| `POT_MUX` (ADC) | D15 |
| `MUX_CTRL_A` | D24 |
| `MUX_CTRL_B` | D25 |
| `MUX_CTRL_C` | D26 |

| Pot | Ref | Mux Channel |
| --- | --- | --- |
| Potentiometer 1 | VR1 | `MUX_CH_0` |
| Potentiometer 2 | VR2 | `MUX_CH_1` |
| Potentiometer 3 | VR3 | `MUX_CH_2` |
| Potentiometer 4 | VR4 | `MUX_CH_3` |
| Potentiometer 5 | VR5 | `MUX_CH_4` |
| Potentiometer 6 | VR6 | `MUX_CH_5` |
| Potentiometer 7 | VR7 | `MUX_CH_6` |
| Potentiometer 8 | VR8 | `MUX_CH_7` |

### Tactile Switches (2 × shift register)

The 16 tactile switches are read through two daisy-chained shift registers (U4, U5).

| Signal | Seed3 Pin |
| --- | --- |
| `SR_LATCH` | D9 |
| `SR_CLK` | D8 |
| `SR_DATA` | D10 |

Switches S1–S16 map to `TAC_SW1`–`TAC_SW16`, arranged in two rows of eight:

| | | | | | | | |
| --- | --- | --- | --- | --- | --- | --- | --- |
| S1 | S2 | S3 | S4 | S5 | S6 | S7 | S8 |
| S9 | S10 | S11 | S12 | S13 | S14 | S15 | S16 |

### LEDs (I²C LED driver)

The 16 LEDs (`LED_1`–`LED_16`, refs D1–D16) sit in the same two rows of eight as the tactile switches, each LED just above its switch (D1 above S1, through D16 above S16), and are driven by a PCA9685 LED driver (U8) over I²C.

| Signal | Seed3 Pin |
| --- | --- |
| `I2C_SCL` | D11 |
| `I2C_SDA` | D12 |

> [!NOTE]
> LED refs D1–D16 are component reference designators, not Seed3 pins.

### Toggle Switches

| Switch | Ref | Signal | Seed3 Pin | Type |
| --- | --- | --- | --- | --- |
| Toggle switch 1 | SW17 | `TOG_1` | D19 | ON-ON |
| Toggle switch 2 | SW18 | `TOG_2_A` / `TOG_2_B` | D28 / D27 | ON-OFF-ON (3-position, two pins) |

### CV & Gates

| Jack | Ref | Signal | Seed3 Pin |
| --- | --- | --- | --- |
| Gate In 1 | J_GATE_IN1 | `GATE_IN1` | D0 |
| Gate In 2 | J_GATE_IN2 | `GATE_IN2` | D18 |
| CV In 1 | J_CV_IN_1 | `CV_IN_1` | D20 |
| CV In 2 | J_CV_IN_2 | `CV_IN_2` | D21 |
| CV Out 1 | J_CV_OUT_1 | `CV_OUT_1` | D23 (DAC channel 1) |
| CV Out 2 | J_CV_OUT_2 | `CV_OUT_2` | D22 (DAC channel 2) |

### MIDI

| Jack | Ref | Signal | Seed3 Pin | Direction |
| --- | --- | --- | --- | --- |
| MIDI Out | J11 | `MIDI_TX` | D13 | Out |
| MIDI In | J10 | `MIDI_RX` | D14 | In |
| MIDI Thru | J8 | `MIDI_RX` | D14 | Mirrors MIDI In |

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

The Dev Kit's USB-C port (J7) powers the board and connects to the Seed3's USB High Speed peripheral. It is separate from the USB-C port on the Seed3 module.

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
- [KiCad Design Files](https://github.com/daisyaudio/Seed3-DevKit-Desktop/releases/latest)

---

## Open-Source Hardware

<img width="256px" height="auto" alt="Open Source Hardware logo" src="https://github.com/user-attachments/assets/f9264744-3509-4cf0-9f4a-981cb05eb38e" />

The Desktop Dev Kit is open-source hardware, built to the [Open Source Hardware Definition](https://www.oshwa.org/definition/) published by the Open Source Hardware Association (OSHWA). The schematics, PCB layouts, bill of materials, and KiCad source files are published so that you can study, modify, manufacture, and sell your own designs based on them.

## License

Copyright © 2026 Qu-Bit Electronix, Inc. (dba Daisy)

The hardware design files in this repository are licensed under the **CERN Open Hardware Licence Version 2 – Permissive** ([CERN-OHL-P-2.0](https://ohwr.org/cern_ohl_p_v2.pdf)).

Subject to the terms of that licence, you may:

- Use, study, copy, modify, and distribute these designs and any products made from them.
- Incorporate these designs, in whole or in part, into closed-source and commercial products.

When you redistribute these designs or products made from them, you must:

- Retain all copyright, licence, and other notices contained in the source files.
- Add a notice to any modified source stating that you modified it, with the date and a brief description of the change.
- Ensure that recipients of any product made from these designs have access to the applicable notices.

These designs are provided "as is", without warranty of any kind, express or implied. See [LICENSE](LICENSE.txt) for the full licence text, including the disclaimer of warranty and limitation of liability.

Firmware and software, including [libDaisy](https://github.com/daisyaudio/libDaisy) and [DaisySP](https://github.com/daisyaudio/DaisySP), are licensed separately under the terms included in their respective repositories.

SPDX-License-Identifier: CERN-OHL-P-2.0

### Trademarks

DAISY® is a trademark of Qu-Bit Electronix, Inc., registered in the United States. CERN-OHL-P-2.0 grants a license to the copyright and related rights in these designs. It does not grant any right or license to use the DAISY name, logos, product names, or other trademarks of Qu-Bit Electronix, Inc.

Products, derivative designs, and related materials made from these designs may not:

- Use the DAISY name or logo, or any confusingly similar name or mark, in a product name, model number, brand, domain name, or marketing material.
- Reproduce the DAISY name or logo on a PCB silkscreen, front panel, enclosure, packaging, or documentation, except where needed to keep the required licence notices.
- State or imply that the product is made, endorsed, sponsored, certified, or supported by Qu-Bit Electronix, Inc.

You may make truthful, factual statements about compatibility or origin, such as "based on the Seed3 Desktop Dev Kit design" or "compatible with the Daisy Seed3", provided the statement does not suggest affiliation or endorsement. If you redistribute or sell products derived from these designs, remove the DAISY name and logo from the silkscreen and other artwork before manufacture.

For trademark licensing or permission requests, contact Qu-Bit Electronix, Inc. through [daisy.audio/pages/support](https://daisy.audio/pages/support).
