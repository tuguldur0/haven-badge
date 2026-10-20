# BOM: Haven game jam badge v2

## Constraints
- Input: 1x AA cell (0.9-1.6 V) via HW-553 boost to 5 V (default); or 3x AA (2.7-4.8 V) straight to D1 with JP2 bridged and U2 omitted; or USB-C 5 V through the RP2040-Zero
- Board size: 100 x 92 mm, 2-layer, 1.6 mm FR4 (PCBWay, bare boards, no assembly). Bottom 17 mm front = 5-slot sponsor logo strip (no routing)
- Back: 48 x 62 mm portrait battery-holder keep-out (board x 32.5-80.5, y 30-92), no pads/legs/screws/footprints inside
- Quantity: 69 badges (order 75-80 for spares)
- IPC class: 2
- Manufacturer min trace: 0.15 mm (design to 0.25 mm signal / 0.5 mm power for hand-soldered boards)
- Assembly: hand soldering by volunteers. Through-hole only (the only SMD feature is the JP1 solder jumper copper)
- Thermal: indoor, worn on a lanyard, no enclosure
- EMI/EMC: none

## Block diagram
```
J5 (AA holder, own switch) --BAT+--> U2 HW-553 boost (IN+/IN-, 5V/GND) --BOOST_5V--> D1 1N5819 --VSYS--> U1 5V pin
                       |  \--JP2 (open; 3xAA: bridge, omit U2)--------/                      |      (USB-C VBUS is tied to VSYS inside U1)
                       +--R1 100k--BAT_SENSE (GP29/ADC3)--R3 100k-- GND, C2 100nF to GND    +-- C1 10uF
U1 RT9013 3V3 --+3V3--> JP1 pad 3, J4
VSYS --> JP1 pad 1 ; JP1 pad 2 --DISP_VCC--> DS1 VCC   (default 1-2 bridged = 5 V)
U1 SPI0 GP2/GP3 + GP4 DC, GP5 CS, GP6 RST --> DS1 ; GP7 --R2 100R--> DS1 BL
8 buttons GPIO -> GND (internal pull-ups) ; J4 expansion (DNP): GND, 3V3, GP13 SCL, GP12 SDA, GP11, GP10, GP9
```

## Components
| Ref | lib_id (verified) | Value | Rating | Package | Stage | Footprint (verified) | Notes |
|-----|-------------------|-------|--------|---------|-------|---------------------|-------|
| U1 | haven:RP2040-Zero (custom) | RP2040-Zero clone (V1083) | 5 V in, 3V3 500 mA LDO | module, socketed | MCU | haven:RP2040-Zero_Socketed (custom) | Needs 2x 1x9 + 1x 1x5 female 2.54 mm headers. If a clone ships without bottom pins, solder 1x5 male pins on it |
| DS1 | haven:GMT020-02-8P (custom) | GMT020-02-8P 2.0" ST7789 240x320 | 3.3-5 V (on-module LDO) | module, socketed | Display | haven:GMT020-02-8P_Socketed (custom) | 1x8 female socket + 4x M2 standoffs + 8x M2 screws (standoff length to be measured: socket + header plastic, about 11 mm) |
| U2 | haven:HW-553 (custom; 1 IN+, 2 IN-, 3 GND, 4 D+, 5 D-, 6 5V) | HW-553 | 0.9-5 V in, 5 V out | module, 34 x 16.2 (listing) | Power | haven:HW-553_Placeholder (custom PLACEHOLDER) | Pad positions unverified until the dimension drawing arrives. IN- and GND both to GND; D+/D- no-connect. Needs at least 200 mA out at 1.0 V in |
| JP2 | Jumper:SolderJumper_2_Open | 3xAA_BYPASS | ~0.2 A | copper jumper | Power | Jumper:SolderJumper-2_P1.3mm_Open_RoundedPad1.0x1.5mm | BAT+ to BOOST_5V. Open by default. Silk: "3xAA: bridge JP2, omit U2". Never bridge with U2 fitted (shorts the boost output to the cell) |
| J5 | Connector_Generic:Conn_01x02 | AA JST-PH | 2 A | JST PH 2.0 THT vertical | Power | Connector_JST:JST_PH_B2B-PH-K_1x02_P2.00mm_Vertical | Pin 1 = BAT+, pin 2 = GND. Big + / - silk marks. Holder has its own switch |
| D1 | Diode:SB120 symbol (pins K=1, A=2), Value 1N5819 | 1N5819 | 40 V 1 A (load about 0.15 A, 15%) | DO-41 | Power | Diode_THT:D_DO-41_SOD81_P10.16mm_Horizontal | Derived symbol 1N5819 cannot be embedded by the MCP server; SB120 has identical pins. Blocks USB back-feed into the boost output |
| C1 | Device:C | 10 uF | 16 V X5R/X7R (5 V rail, 31%) | radial MLCC, 5 mm pitch | Power | Capacitor_THT:C_Disc_D5.0mm_W2.5mm_P5.00mm | Non-polarised on purpose |
| R1 | Device:R | 100k | 1/4 W | axial DIN0207 | Batt sense | Resistor_THT:R_Axial_DIN0207_L6.3mm_D2.5mm_P10.16mm_Horizontal | BAT+ to BAT_SENSE (divider top) |
| R3 | Device:R | 100k | 1/4 W | axial DIN0207 | Batt sense | Resistor_THT:R_Axial_DIN0207_L6.3mm_D2.5mm_P10.16mm_Horizontal | BAT_SENSE to GND (divider bottom). Vadc = Vbat/2: 1xAA max 0.8 V, 3xAA max 2.4 V (< 3.3 V). Drain 8-24 uA, only while the holder switch is on |
| C2 | Device:C | 100 nF | 16 V+ | radial MLCC, 5 mm pitch | Batt sense | Capacitor_THT:C_Disc_D5.0mm_W2.5mm_P5.00mm | ADC filter, source 50k, RC = 5 ms |
| R2 | Device:R | 100R | 1/4 W | axial DIN0207 | Display | Resistor_THT:R_Axial_DIN0207_L6.3mm_D2.5mm_P10.16mm_Horizontal | GP7 PWM to DS1 BL (module switches the backlight with its own Q2) |
| JP1 | Jumper:SolderJumper_3_Bridged12 | VCC_SEL | n/a | copper jumper | Display | Jumper:SolderJumper-3_P1.3mm_Bridged12_RoundedPad1.0x1.5mm_NumberLabels | 1 = VSYS, 2 = DISP_VCC, 3 = +3V3. Default 1-2 bridged (5 V) |
| SW1-SW8 | Switch:SW_Push | UP, DOWN, LEFT, RIGHT, A, B, START, SELECT | 12 V 50 mA | 6x6 mm THT tact | Buttons | Button_Switch_THT:SW_PUSH_6mm | GPIO to GND, internal pull-ups. D-pad on 9.5 mm centres; START/SELECT stacked in the right column above A (moved out of the battery keep-out) |
| J4 | Connector_Generic:Conn_01x07 | EXPANSION (DNP) | n/a | 1x7 2.54 mm | Expansion | Connector_PinHeader_2.54mm:PinHeader_1x07_P2.54mm_Vertical | Unpopulated. GND, 3V3, GP13 SCL, GP12 SDA, GP11, GP10, GP9 |
| #FLG01-04 | power:PWR_FLAG | - | - | - | ERC | - | BAT+, GND, VSYS, DISP_VCC |

Not on the BOM but needed per badge: 2x 1x9 + 1x 1x5 + 1x 1x8 female 2.54 mm headers, 4 M2 standoffs + 8 M2 screws, a 1xAA holder with switch and JST-PH lead, foam tape, lanyard.
Lanyard slot and title/name-box silkscreen are board features, added at layout.

## GPIO map
| GPIO | U1 pad | Net |
|------|--------|-----|
| GP0 | 23 | BTN_A |
| GP1 | 22 | BTN_B |
| GP2 | 21 | LCD_SCK (SPI0 SCK) |
| GP3 | 20 | LCD_MOSI (SPI0 TX) |
| GP4 | 19 | LCD_DC |
| GP5 | 18 | LCD_CS_N |
| GP6 | 17 | LCD_RST_N |
| GP7 | 16 | LCD_BL_PWM (PWM3 B) |
| GP8 | 15 | BTN_START |
| GP9 | 14 | EXP_GP9 |
| GP10 | 13 | EXP_GP10 |
| GP11 | 12 | EXP_GP11 |
| GP12 | 11 | EXP_SDA (I2C0) |
| GP13 | 10 | EXP_SCL (I2C0) |
| GP14 | 9 | BTN_SELECT |
| GP15 | 8 | BTN_RIGHT |
| GP26 | 7 | BTN_LEFT |
| GP27 | 6 | BTN_DOWN |
| GP28 | 5 | BTN_UP |
| GP29 | 4 | BAT_SENSE (ADC3) |
| 3V3 | 3 | +3V3 |
| GND | 2 | GND |
| 5V | 1 | VSYS |
| GP16 | (no pad) | on-module WS2812, unused |

## Custom footprint dimensions
### haven:RP2040-Zero_Socketed (origin = module centre, USB end toward -Y)
Source: parts/module/800px-900px-RP2040-Zero-details-size.jpg (Waveshare drawing)
| Dimension | Value | Source |
|-----------|-------|--------|
| Module outline | 18.00 x 23.50, R1.00 corners | drawing |
| Side-row pin centre from long edge | 1.38 (so rows are 15.24 apart, pads at x = +/-7.62) | drawing |
| First side pin from USB edge | 1.59 (pads at y = -10.16) | drawing |
| Pin pitch | 2.54 | drawing |
| Bottom row: GP9 from right edge | 3.92 (pads at x = -5.08 .. +5.08) | drawing |
| Bottom row Y | 21.91 from USB edge (y = +10.16), level with GP14/GP8 | INFERRED, not dimensioned. Measure a clone |
| USB-C body (Fab only) | 4.39 from left edge, 4.67 from right edge, overhangs the USB edge by 1.5 | drawing; overhang from photo (reviewer) |
| Courtyard | 1.75 past the USB edge (1.5 overhang + 0.25), also covers the 5V/GND silk at left | review fix |
| Silk | outline, "USB-C", pin-1 bar, "5V" and "GND" left of pads 1 and 2 (outside the module, visible when fitted) | review fix |
| Pad / drill | 1.7 mm round (pad 1 square) / 1.0 mm | KiCad PinSocket_1x09_P2.54mm_Vertical |
| Pad numbering | 1-9 left row 5V, GND, 3V3, GP29, GP28, GP27, GP26, GP15, GP14; 10-14 bottom GP13..GP9; 15-23 right row GP8..GP0 (counter-clockwise from the top) | pinout image details-7.jpg |

### haven:GMT020-02-8P_Socketed (origin = module centre, pins toward -Y, front view)
Source: parts/display/brave_tNfNKGqZeg.png (Goldenmorning drawing)
| Dimension | Value | Source |
|-----------|-------|--------|
| PCB outline | 37.10 x 62.00 | drawing |
| Mounting holes, spacing | 28.00 (X) x 58.00 (Y) | drawing |
| Hole centre from side edge | 4.55 (drawing prints 4.5; (37.10-28.00)/2 = 4.55 used) | derived |
| Hole centre from top/bottom edge | 2.00 | drawing ("2"; (62-58)/2) |
| Module hole diameter | 2.50 | drawing |
| Badge hole | 2.40 NPTH (M2 clearance with margin) | design choice (review fix) |
| Silk | pin names GND VCC SCL SDA RST DC CS BL, 1.0 mm text rotated 90, below each pad; "1" at pin 1 | review fix |
| Pin 1 centre from left edge | 9.30 | drawing |
| Pin 8 centre from right edge | 10.02 (check: 9.30 + 17.78 + 10.02 = 37.10) | drawing |
| Pin row from top edge | 2.80 (0.8 below the hole centres) | drawing ("2.8"), confirmed on enlarged crop |
| Pin pitch | 2.54, 7 x 2.54 = 17.78 | drawing |
| Pad / drill | 1.7 mm round (pad 1 square) / 1.0 mm | KiCad PinSocket_1x08_P2.54mm_Vertical |
| Active area (Fab only) | 30.6 x 40.8, top at 5.4 + 2.44 from top edge | drawing |

### haven:HW-553_Placeholder (origin = module centre) - PLACEHOLDER, replace from the drawing
Source: parts/boost/*.png (listing photos only, not a dimensioned drawing)
| Dimension | Value | Source |
|-----------|-------|--------|
| Outline | 34.0 x 16.2 | listing photo arrows. The photo proportions look closer to about 25 x 16; caliper one module |
| Pads 1 IN+, 2 IN- | (14.5, +5.6), (14.5, -5.6), 1.7 / 1.0 drill | topology from photo only (IN+/IN- corner pads at one short end) |
| Pads 3 GND, 4 D+, 5 D-, 6 5V | x = -12.0, y = -3.81 / -1.27 / +1.27 / +3.81 (2.54 pitch assumed) | topology from photo only (USB-A style row at the other end) |
| Courtyard | outline + 0.25 | reserves the area |
