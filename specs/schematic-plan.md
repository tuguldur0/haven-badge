# Schematic plan: haven-badge v2

Single sheet, A4 landscape (297 x 210). 27 symbols + 4 PWR_FLAG.
Page check: widest row is power chain J5..C1 (x 30..115) plus U1 (x 150) plus DS1 (x 235) -> max x 245 < 287;
buttons at y 135/160 -> max y 165 < 178 (title block starts at y 178, x 189). Fits A4.

All connections use net labels (`wire_pins_to_net`), no long wires.

## Placement
| Ref | lib_id | Value | x | y | rot | Stage |
|-----|--------|-------|---|---|-----|-------|
| J5 | Connector_Generic:Conn_01x02 | 1xAA JST-PH | 30.48 | 40.64 | 0 | Power |
| U2 | haven:BoostModule_TBD | BOOST_TBD | 63.5 | 40.64 | 0 | Power |
| D1 | Diode:1N5819 | 1N5819 | 96.52 | 38.1 | 180 | Power |
| C1 | Device:C | 10uF | 116.84 | 45.72 | 0 | Power |
| R1 | Device:R | 10k | 38.1 | 76.2 | 0 | Batt sense |
| C2 | Device:C | 100nF | 55.88 | 76.2 | 0 | Batt sense |
| U1 | haven:RP2040-Zero | RP2040-Zero | 152.4 | 88.9 | 0 | MCU |
| JP1 | Jumper:SolderJumper_3_Bridged12 | VCC_SEL | 205.74 | 40.64 | 0 | Display |
| DS1 | haven:GMT020-02-8P | GMT020-02-8P | 238.76 | 76.2 | 0 | Display |
| R2 | Device:R | 100R | 205.74 | 99.06 | 0 | Display |
| SW1-SW4 | Switch:SW_Push | UP/DOWN/LEFT/RIGHT | 30.48..106.68 step 25.4 | 134.62 | 0 | Buttons |
| SW5-SW8 | Switch:SW_Push | A/B/START/SELECT | 30.48..106.68 step 25.4 | 160.02 | 0 | Buttons |
| J4 | Connector_Generic:Conn_01x07 | EXPANSION (DNP) | 238.76 | 134.62 | 0 | Expansion |

## Wiring (one wire_pins_to_net call per net, power first)
| Net | Pins |
|-----|------|
| GND | J5:2, U2:2, U2:4, C1:2, C2:2, U1:2, DS1:1, SW1..SW8:2, J4:1 |
| BAT+ | J5:1, U2:1, R1:1 |
| BOOST_5V | U2:3, D1:A |
| VSYS | D1:K, C1:1, U1:1, JP1:1 |
| +3V3 | U1:3, JP1:3, J4:2 |
| DISP_VCC | JP1:2, DS1:2 |
| BAT_SENSE | R1:2, C2:1, U1:4 (GP29/ADC3) |
| LCD_SCK | U1:21 (GP2), DS1:3 |
| LCD_MOSI | U1:20 (GP3), DS1:4 |
| LCD_DC | U1:19 (GP4), DS1:6 |
| LCD_CS | U1:18 (GP5), DS1:7 |
| LCD_RST | U1:17 (GP6), DS1:5 |
| LCD_BL_PWM | U1:16 (GP7), R2:1 |
| LCD_BL | R2:2, DS1:8 |
| BTN_A / BTN_B | U1:23 (GP0) SW5:1 / U1:22 (GP1) SW6:1 |
| BTN_START | U1:15 (GP8), SW7:1 |
| BTN_SELECT | U1:9 (GP14), SW8:1 |
| BTN_UP/DOWN/LEFT/RIGHT | U1:5 (GP28) SW1:1, U1:6 (GP27) SW2:1, U1:7 (GP26) SW3:1, U1:8 (GP15) SW4:1 |
| EXP_SCL / EXP_SDA | U1:10 (GP13) J4:3 / U1:11 (GP12) J4:4 |
| EXP_GP11/10/9 | U1:12 J4:5 / U1:13 J4:6 / U1:14 J4:7 |

## Power flags
| Net | Reason |
|-----|--------|
| GND | only power_in + passive pins |
| BAT+ | battery connector is passive |
| VSYS | fed through passive D1 (and USB inside U1, invisible to ERC) |
| DISP_VCC | fed through passive JP1 |

## No-connect
None. Every U1 pin is used (GP16-25 are not on the headers).
