# HK-10 Design Notes

This document records relevant design decisions and considerations for the HK-10 motherboard,
organized by functional area. It complements the schematics and [README.md](README.md) by
capturing the *why* behind choices that aren't obvious from the design files alone.

## Power management

### AC/DC adapter

The HK-10 integrates an AC/DC power adapter inside the enclosure rather than relying on an
external wall-wart, unlike the Model 101 prototype. The adapter is a third-party module, not
a custom design: the [Meanwell RT-50B][1] was chosen. It provides the 5V, 12V and -12V
rails required by the board.

The adapter connects to the motherboard through a cable fitted with a JST connector.

The RT-50B is a switching (SMPS) power supply, so its output rails can carry significant
ripple and switching noise. On its own 5V rail, this noise is prone to leaking into the VDP's
analog video output, resulting in a visibly noisy picture. To mitigate this, we are evaluating
deriving a clean 5V supply for the VDP from the RT-50B's 12V rail through a linear regulator
(7805) instead of using the RT-50B's 5V rail directly. A linear regulator rejects the
switching noise present on the 12V rail, at the cost of the extra heat dissipated by the
regulator. This is not settled yet, but the 7805 would most likely live on the HKG-99xx
graphics card rather than on the motherboard.

### Power-on reset (PoR)

The power-on reset circuit is built around the [MIC1232][2] from Microchip. It was
chosen mainly for its ease of use combined with good availability, particularly in DIP
package, which matters for a kit meant to be hand-assembled by end users.

## Clock generation

The master clock is generated with a packaged crystal oscillator IC, the Epson SG-615P /
SG-531P, instead of a discrete crystal driven by an unbuffered inverter (74HCU04) with its
typical feedback network (bias/feedback resistor, series damping resistor, and symmetric
load capacitors). The oscillator IC integrates the crystal, feedback network and load
capacitors internally and outputs a ready-made digital clock signal, which removes several
discrete components (crystal, two load capacitors, two resistors) and the oscillator gate
from the board. The divide-by-2 stage that derives `PSGCLK` from `CLK` for the PSG is
unaffected by this change.

The oscillator's `CLK` output is buffered before fanning out to the CPU, the cartridge
slots, the power-on reset sync logic and the `PSGCLK` divider, rather than driving all of
that directly from the oscillator's output pin. Buffering keeps the oscillator isolated
from a net that is exposed on the (hot-pluggable) cartridge slot connectors, and gives
some headroom against the oscillator's fairly light guaranteed output drive (`IOH` = -400
µA min. per the datasheet). The buffer is a spare gate on `U14`, a `74HC08` (quad 2-input
AND) already used for address-decoding logic, wired as a non-inverting buffer by tying one
input permanently high. This avoids adding a new IC just for buffering — none of the
existing inverter packages on the board had a spare gate, but this AND gate did. Signal
polarity doesn't matter here (nothing on this board has a phase relationship to preserve
against the raw oscillator output), so a single gate is enough; there's no need for a
second inversion. Checked against the TI SN74HC08 datasheet, this buffer neither hurts
fanout (both parts are rated for 10 LSTTL loads) nor meaningfully degrades the clock edges
(its rise/fall time is comparable to the oscillator's own, and negligible next to the
~279 ns clock period at 3.579545 MHz).

## Enclosure layout

The enclosure targets a 25x30cm internal perimeter (this may grow depending on the final
enclosure design and wall thickness). The motherboard, at 24x18cm, sits centered and pushed
against the front end of the enclosure.

On that front end are the joystick ports and the keyboard port. The cassette port sits on the
right-hand side. The cartridge slots are centered on the motherboard.

The rear end of the enclosure houses the RT-50B power supply and the HKG-99xx graphics card.
The motherboard and the graphics card are connected by a 50-pin IDC cable.

---

[1]: http://www.meanwelljapan.com/upload/pdf/RT-50/RT-50-SPEC.PDF "Meanwell RT-50B datasheet"
[2]: https://www.microchip.com/en-us/product/mic1232 "Microchip MIC1232 product page"
