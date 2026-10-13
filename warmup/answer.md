# Serial Input 8 Bit Adder with Fixed Value Match Detector

## Project Description

This board is a **serial input 8 bit adder with a fixed value match detector**. Two single bit data streams (Input 1 and Input 2) are clocked into two separate 8 bit serial in/parallel out shift registers. Once eight bits have been loaded, the two bytes are added by a cascaded 8 bit binary adder. The sum goes to a hard-wired equality comparator, which raises the output **F** only when the sum equals a predetermined constant.

## Functional Blocks

### Power Supply
A USB-C connector with 5.1 kΩ CC pull-downs supplies 5 V. A TC1262 linear regulator then provides the 3.3 V logic rail, with 1 µF and 10 µF bulk decoupling.

### Clock
A crystal oscillator module (Y2) generates the system clock (CLK) for all flip-flops. It has an enable pin and a status LED.

### Reset and Enable
A push button (SW2) with a 100 nF debounce capacitor produces RST. It is combined with the EN signal through a 2-input AND gate (U21) to create a qualified reset, R_RST.

R_RST drives 14 NPN transistors, each with a 10 kΩ base resistor and a 10 kΩ pull-down. The transistors pull the register outputs Q0–Q6 low on each channel, which clears the registers. This is needed because the D flip-flops used have no clear pin.

### Data Capture
Two chains of eight cascaded D flip-flops (U1–U8 and U10–U17) form two 8-bit shift registers. On each clock edge, the input bit moves one stage along. After eight clocks, each register holds a full byte in parallel:

- **Operand A:** Q0–Q7 from Input 1
- **Operand B:** Q0–Q7 from Input 2

### Arithmetic
Two 4-bit full adders are chained carry-out to carry-in to form an 8-bit ripple-carry adder:

- **U22:** low nibble
- **U23:** high nibble

The first carry-in is tied low through R25. The result is S0–S7 with a final carry out (COUT).

### Comparison
The target value is set by the wiring. Sum bits S0, S3 and S6 go through inverters (U19, U24, U20). Those inverted bits and the remaining sum bits all feed a multi-input AND gate (U18). The gate's output **F** goes active only when every bit matches the pattern:

| Bit   | S7 | S6 | S5 | S4 | S3 | S2 | S1 | S0 |
|-------|----|----|----|----|----|----|----|----|
| Value | 1  | 0  | 1  | 1  | 0  | 1  | 1  | 0  |

**Target value: 1011 0110 = 0xB6 = 182 (decimal)**

## One-Line Summary

> A serial-to-parallel 8 bit accumulator and adder: two bitstreams are shifted into separate 8-bit registers, summed by a cascaded 4 bit adder pair, and checked against a hard-wired 8 bit constant (0xB6) by a gate level equality comparator that outputs a match flag.
