# Artemisa MSX - Model HK-10 Motherboard

This repository contains the KiCad project files for the Model HK-10 motherboard, part of the **Artemisa** project.

## About Artemisa

Artemisa is a project to design new MSX-1 compatible computers using modern manufacturing
techniques while staying true to the original architecture: a Z80 CPU, discrete memory chips,
and dedicated peripheral ICs (VDP, PSG, PPI, etc.), with no FPGAs involved. The roadmap
includes several different models, each exploring different trade-offs between fidelity,
cost, and ease of assembly.

## The HK-10 model

The HK-10 is based on the **Model 101**, a prototype that was never distributed commercially.
The goal of the HK-10 is to bring that design to the general public as a homebrew-friendly
product, with the constraints and trade-offs typical of the homebrew scene.

"HK" stands for **Homebrew Kit**. The HK-10 is meant to be sold as a kit and assembled by the
user, comprising:

- The motherboard and other PCBs
- A 3D-printed PETG enclosure
- The main chips (CPU, memory, PSG, VDP)
- Passive components (resistors, capacitors, etc.)

### Features

- 2 cartridge slots
- 1 datasette (cassette) port
- 2 joystick ports
- External, cable-connected keyboard port

The HK-10 does **not** include a serial port or a printer port.

### Graphics

The graphics subsystem is implemented on separate PCBs rather than being part of the
motherboard itself, so that users can customize their video output and choose a graphics
chip (TMS9918 vs TMS9928 vs TMS9929). These graphics cards are based on the GFX-9918 card
used in the Model 101 prototype, but with a different form factor and naming, e.g.
`HKG-9918`, `HKG-9929`.

### Memory

The HK-10 uses SRAM instead of DRAM. For the memory sizes involved, SRAM is simpler, cheaper
and easier to source than DRAM, at the cost of some board space.

### Design philosophy

The HK-10 aims to be the simplest and most didactic model in the Artemisa lineup. To that
end, it avoids any programmable logic devices (PLDs, PALs, GALs, etc.) in favor of discrete
logic chips, consistent with how the original MSX computers were designed in the 1980s.

### Differences from the Model 101 prototype

- **Form factor**: the HK-10 uses a different, more compact form factor than the 101.
- **Power supply**: the HK-10 has its own integrated AC/DC adapter providing 5V, 12V and -12V,
  whereas the 101 was only powered externally at 5V.
- **Simplified circuits**: some circuits have been simplified compared to the 101, namely the
  clock generation and the power-on reset (PoR) circuit.
- **No integrated keyboard**: unlike most MSX computers (and most microcomputers of that era),
  the HK-10 does not have a keyboard built into the case. The keyboard is a separate unit
  connected via cable to a port on the motherboard.

## Repository layout

The schematic is split into sheets by functional block:

| File | Contents |
|---|---|
| `motherboard-hk10.kicad_sch` | Top-level sheet |
| `cpu.kicad_sch` | Z80 CPU |
| `ram.kicad_sch` | SRAM |
| `rom.kicad_sch` | ROM |
| `ppi.kicad_sch` | Programmable Peripheral Interface (keyboard, joysticks, cassette control) |
| `psg.kicad_sch` | Programmable Sound Generator |
| `clock.kicad_sch` | Clock generation |
| `decoding.kicad_sch` | Address decoding |
| `power.kicad_sch` | Power supply and power-on reset |
| `slots.kicad_sch` | Cartridge slots |
| `keyboard.kicad_sch` | External keyboard connector |
| `cassette.kicad_sch` | Datasette port |

`motherboard-hk10.kicad_pcb` contains the PCB layout, and `simulation/` contains SPICE
simulations used to validate specific circuits (power-on reset, clock generation, video
output filtering, etc.).

See [CHANGELOG.md](CHANGELOG.md) for a history of board revisions.

## Key components

- **Clock generator**: [Epson SG-615P / SG-531P][1], a packaged crystal oscillator (SPXO)
  used to generate the master clock.

## License

This project is licensed under the [TAPR Open Hardware License](LICENSE.txt).

---

[1]: https://www.mouser.com/datasheet/2/137/SG_615P_en-1880245.pdf "Epson SG-615P / SG-531P datasheet"
