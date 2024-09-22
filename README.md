# DSi modchip PCB and flex-cable

> [!IMPORTANT]
> ***This PCB is still a work-in-progress and has bugs.***

This repository contains the KiCAD design files for the DSi modchip and the
cable flex-PCB.

## Notes

Currently, you'll have to order and make these yourself. I don't think making
them widely available already is a good idea, there's still a few bugs left in
the design.

The issues with the modchip PCB itself are listed [here](/wifi-interposer/TODO-r2.2.).
Additionally, inserting a cable into the modchips USB socket too forcefully can
cause the modchip PCB to snap in half, which is bad.

I haven't actually gotten the flex-cable to work properly. There are problems
with either a too large parasitic capacitance on the glitch signal line, or bad
contacts between the flexPCB and the glitching MOSFET. I suggest deadbugging
the crowbar MOSFET instead, if that's something you're capable of.

## Ordering/fab instructions

The modchip is a 4-layer board that should be 0.8 mm thick. I've gotten it
fabbed successfully from both Aisler (at 1.6mm thickness) and JLCPCB.

The flex-cable consists of two layers, needs stiffner in certain places, and
has been fabbed from JLCPCB.

## Bill of Materials

### Modchip main components

These are all the components required for the core functionality of the modchip.

| Reference | Value       | Footprint | Qty | Farnell n° | LCSC n° | Mouser n° |
|:--------- |:----------- |:--------- | ---:| ----------:| -------:|:--------- |
| C3,C4     | 27pF C0G    | 0402      | 2   | 4539081    | C880615 | 603-CC402GRNPO9BN270 |
| C5,C6,C7,C10,C11,C12,C13,C14,C15,C16,C17 | 100nF | 0402 | 11 | 1758880 | C16772 | 791-0402B224K100CT |
| C8,C9     | 1uF         | 0402      | 2   | 3013332    | C12530  | 187-CL05A105KP5NNNC |
| J1        | Molex SlimStack 501920-5001 | | 1 | N/A    | N/A     | 538-501920-5001 |
| J5        | Molex 200528-0040 |     | 1   | 3500399    | N/A     | 538-200528-0040 |
| Q4        | Dual NPMOS  | SOT-363/SC-70-6|1|3828400    | C155499 | 621-DMC3400SDW-13 |
| R1,R2     | 10k         | 0402      | 2   | 2787800    | C25744  | 279-CRG0402J10K/10 |
| R5        | 47k         | 0402      | 2   | 4004532    | C25792  | 279-CRG0402J47K |
| R7,R8     | 1k          | 0402      | 2   | 4004531    | C11702  | 603-RC0402FR-131KL |
| SW1, SW2  | 1P1T button | 4.2x3.2mm | 2   | 3703693    | C2689510| 611-RKB2SJM250SMTRLF |
| U1        | RP2040      | QFN-56 P0.4 | 1 | 3766082    | C2040   | 358-SC09147 |
| U2,U4     | W25Q16JVUXIQ | DFN-8 P0.5mm |2| N/A        | C2983691| 454-W25Q16JVUXIQTR |
| Y2        | 12 MHz 18pF | SMD3225 GND2/4 | 1 | 3572992 | C7424717| 774-403C35D12M00000 |


> [!NOTE]
> `J5` is optional, and is only needed when you use the flex-cable. It can be
> omitted when deadbugging the crowbar MOSFET.

> [!TIP]
> * For Q4, several compatible components exist: Diodes DMC{2710U,3400S,3401L}DW,
>   Toshiba SSM6L35FU.
> * For SW1 and SW2, you can use one of the following:
>   * C&K {RKB,PTS81[05]}2SJ[GKM]250SMTR LF
>   * Alps Alpine SKRPABE010
>   * E-Switch TL3365AF180QG
>   * CUI TS20-42-25-WT-160-SMT-TR
>   * Wurth 434123025826 or 434133025816
> * For U1, you could try using the RP2350A **once its erratum E9 has been
>   fixed in hardware**.
> * For U2 and U4, any similarly-sized 3.3V >100MHz QSPI flash chip should work.
>   Requirements on U4 are lower (only 1-bit SPI, 16 MHz) but it's often cheaper
>   to buy the same chip in bulk.
> * For Y2, try e.g. ECS-120-18-33 or ABM8(G)-12.000MHZ-18

### Modchip optional: USB connector

> [!IMPORTANT]
> You most likely want this, though *technically* everything can be done using
> an SWD debugger instead.

| Reference | Value       | Footprint | Qty | Farnell n° | LCSC n° | Mouser n° |
|:--------- |:----------- |:--------- | ---:| ----------:| -------:|:--------- |
| C1,C2     | 1uF         | 0402      | 2   | 3013332    | C12530  | 187-CL05A105KP5NNNC |
| F1        | 0.5A        | 0603      | 1   | 2850001    | C163116 | 530-C2Q500 |
| J3        | USBC 2.0 Recp | USBC 16P| 1   | 3819289    | C428463 | 640-USB4520031A |
| R3,R11    | 47k         | 0402      | 2   | 4004532    | C25792  | 279-CRG0402J47K |
| R6,R12    | 5.1k        | 0402      | 2   | 2447201    | C25905  | 708-RMCF0402FT5K10 |
| R9,R10    | 27 1%       | 0402      | 2   | 4014781    | C100623 | 791-RMC1/16SK27R0FTH |
| U6        | AP2127K-3.3 | SOT-23-5  | 1   | 2510377    | C81233  | 998-MIC5504-3.3YM5TR |
| U3        | D1213A-02SM | SOT-23-5  | 1   | 3946109    | N/A     | 621-D1213A-02SM-7 |
| U7        | USBLC6-2SC6 | SOT-23-6  | 1   | 1269406    | C2827654| 511-USBLC6-2SC6 |

> [!IMPORTANT]
> Use only one of `U3` or `U7`, never both!

### Modchip optional: LED

It indicates the status, except it doesn't work because there's no proper 5V
rail available when plugged into the DSi.

| Reference | Value       | Footprint | Qty | Farnell n° | LCSC n° | Mouser n° |
|:--------- |:----------- |:--------- | ---:| ----------:| -------:|:--------- |
| D1        | WS2812B     | PLCC-2020 | 1   | N/A        | C965555 | 485-4684  |
| R13       | 330         | 0402      | 1   | TODO       | TODO    | TODO      |

### Modchip optional: WiFi module carrier

With a few extra components, you can make the modchip act as a real interposer
PCB between the DSi motherboard and the WiFi module.

| Reference | Value       | Footprint | Qty | Farnell n° | LCSC n° | Mouser n° |
|:--------- |:----------- |:--------- | ---:| ----------:| -------:|:--------- |
| C18,C19   | 1uF         | 0402      | 2   | 3013332    | C12530  | 187-CL05A105KP5NNNC |
| J2        | Molex SlimStack 52991-0508 | | 1 | 3500613 | N/A     | 538-52991-0508 |
| U5        | MIC5317-1.8YD5 | SOT-23-5 | 1 | 2809977    | C236669 | 998-MIC5504-1.8YM5TR |

> [!NOTE]
> Only short `JP1` if you intend to talk to the WiFi module in a standalone
> (i.e. non-interposer) manner.

> [!TIP]
> Instead of a MIC5317, many other SOT-23-5 LDOs such as the MIC5504-1.8YM5 can
> be used instead. I tend to pick ones with a low quiescent current. **Do pay
> attention to the pinout, though, as it sometimes changes even between
> SOT-23-5 parts of the same vendor.**

### Modchip optional: I2C level shifter

This can be a neat extra if you want to talk to the DSi's I2C bus. This was
used during modchip development, but isn't required at runtime.

| Reference | Value       | Footprint | Qty | Farnell n° | LCSC n° | Mouser n° |
|:--------- |:----------- |:--------- | ---:| ----------:| -------:|:--------- |
| C21       | 100nF       | 0402      | 1   | 1758880    | C16772  | 791-0402B224K100CT |
| R4        | 200k        | 0402      | 1   | 3537789    | C25764  | 791-WR04X2003FTL |
| U8        | PCA9306DC1  | VSSOP-8   | 1   | 2400482    | C33196  | 595-PCA9306DCUR |

> [!TIP]
> Instead of a PCA9306 (from either TI or NXP), an LSF0102 (from TI or
> Nexperia) or Nexperia NCA9306 can be used as well.

### Flex-cable

The flex-cable only needs a single IRFHS8342PbF as crowbar MOSFET. There's also
a footprint for a small 0402 series resistance at the gate (e.g. 200 Ohm), but
it can be shorted.

## Installing the modchip, flashing firmware and payload

See [the guide](https://github.com/dsi-modchip/guide?tab=readme-ov-file#dsi-modchip-guide).

## License

[CERN OHL-S v2](/LICENSE)

