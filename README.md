# 🌬️ MelloBand

*A tiny wearable that teaches little lungs how to chill out.*

**MelloBand v1.0 (2026)**, designed & built by Adie Guo

---

## What is it?

MelloBand is a wearable breathing buddy for kids. Strap it on, and when big feelings show up, it walks them through a breathing exercise with a gentle glow and a friendly buzz. No screens, no apps, just a soft pulse to follow.

### Features
- 🌈 **Pulsating RGB LEDs**: breathe in, glow brightens; breathe out, glow softens. A visual rhythm to follow instead of a countdown.
- 🎵 **Patterned haptic buzzes**: three motors work together to give each breathing phase (inhale, hold, exhale) its own distinct feel.
- 🔘 **One-button mode toggle**: simple, kid-proof interaction. No menus to get lost in.
- 🔋 **Rechargeable over USB**: soldered-on battery, standard USB charging, an RGB charge-status light so it's always obvious when it needs a top-up.
- 🧠 **Custom firmware brain**: an STM32F413 running a phase-based state machine, so exercises can be tuned, extended, or swapped without touching hardware.

---

## Project Structure

```
Melloband.PrjPcb
├── Main.SchDoc
│   ├── Power.SchDoc
│   ├── Analog Input Conditioning.SchDoc
│   ├── Interface.SchDoc
│   ├── MCU-io.SchDoc
│   ├── MCU-power.SchDoc
│   └── Haptic Drivers.SchDoc
├── PCB2.PcbDoc
└── Melloband.BomDoc
```

## Hardware Overview

### The Brain, MCU

The system is built around the STM32F413RGT6, a 32-bit ARM MCU with 1MB of flash in a 64-LQFP package. It is clocked by a 25 MHz crystal (X1A/X1B) with 10pF load capacitors, and exposes a JTAG header for programming and debug. Boot0/Boot1 are pulled low for normal boot; pulling BOOT0 high externally forces entry into the system bootloader. Power rails are fully decoupled (five 100nF caps across the VDD pins, a bulk 10uF cap, and VDDA-specific decoupling), and a low-ESR 4.7uF capacitor is required on VCAP_1 for the internal regulator.

### The Fuel, Power

Charging is handled through a USB-C input, protected by a TVS diode (SMAJ5.0A) against ESD. An MCP73831 charge management IC manages the soldered-on Li-ion/LiPo cell, with its status output buffered out to the RGB charge indicator. A MIC5219 LDO regulates the raw battery voltage down to a stable 3.3V system rail, with input protection provided by a 500mA fuse.

### The Calm-Down Button, Analog Input Conditioning

The mode toggle button signal passes through a diode clamp for protection, followed by an RC low-pass filter (10kΩ / 47nF, cutoff around 33.8 Hz) to debounce the switch. The signal is then buffered through two op-amp stages before reaching the MCU, ensuring a clean, reliable input regardless of switch bounce.

### The Face, Interface

Two RGB LEDs indicate mode and charge status, each channel current-limited by 100Ω resistors. A separate single-color LED reflects MCU status for debug purposes. The user-facing controls consist of one tactile mode-toggle button and one slide power switch.

### The Buzz, Haptic Drivers

Three DRV2605L haptic driver ICs control Motors A, B, and C. All three drivers share a single PWM control signal, allowing the firmware to drive coordinated vibration patterns across the motors for each breathing phase. Each driver has its own supply decoupling to keep the haptic output clean and consistent.

## Firmware

Firmware lives in a companion repository: a phase-based state machine that choreographs the LEDs and haptic motors through each breathing exercise.

## Revision

- **v1.0** (2026): first board spin, first breaths 🌱
