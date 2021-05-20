# Registers

### Abbreviations

In the following sections we will use the following abbreviations:

- **LPF** = low pass filter
- **FSR** = force sensitive resistor, the technology used to digitize pressure information
- **FL** = front left, referring tot the FSR located on the front and left side of the sole as seen from above
- **FR** = front right, referring tot the FSR located on the front and right side of the sole as seen from above
- **BL** = back left, referring tot the FSR located on the back and left side of the sole as seen from above
- **BR** = back right, referring tot the FSR located on the back and right side of the sole as seen from above

## Registers table

SPR2010 uses the following registers:

Address | Size (Bytes) | Data Name                                  | Memory | Access |Initial<br/>Value | Range | Unit
:------:|:------------:|--------------------------------------------|:------:|:------:|-------:|:---------:|------|
0       | 2            | Model Number                               | EEPROM | R      |0x5301  | -         | -
6       | 1            | Firmware Version                           | EEPROM | R      | 0x02   | -         | -
7       | 1            | ID                                         | EEPROM | RW     | 100    | 0 ~ 252   | -
8       | 1            | [Baud Rate](#baud-rate)                    | EEPROM | RW     | 3      | 0 ~ 7     | -
9       | 1            | [Protocol Version](#protocol-version)      | EEPROM | RW     | 2      | 1 ~ 2     | -
13      | 1            | Return Delay Time (*not implemented at the moment*) | EEPROM | RW     | 0      | 0 ~ 254   | -
14      | 1            | [Low pass filter scaler](#low-pass-filter-scalers) for FSR| EEPROM | RW     | 15     | 1 ~ 255   | -
15      | 1            | [Low pass filter scaler](#low-pass-filter-scalers) for Voltage/Current| EEPROM | RW     | 30     | 1 ~ 255   | -
20      | 2            | Calibration multiplier FL                  | EEPROM | RW     | 1      | 1 ~ 255   | -
22      | 2            | Calibration divisor FL                     | EEPROM | RW     | 1      | 1 ~ 255   | -
24      | 2            | Calibration multiplier FR                  | EEPROM | RW     | 1      | 1 ~ 255   | -
26      | 2            | Calibration divisor FR                     | EEPROM | RW     | 1      | 1 ~ 255   | -
28      | 2            | Calibration multiplier BL                  | EEPROM | RW     | 1      | 1 ~ 255   | -
30      | 2            | Calibration divisor BL                     | EEPROM | RW     | 1      | 1 ~ 255   | -
32      | 2            | Calibration multiplier BR                  | EEPROM | RW     | 1      | 1 ~ 255   | -
34      | 2            | Calibration divisor BR                     | EEPROM | RW     | 1      | 1 ~ 255   | -
64      | 1            | Enable device                              | RAM    | RW     | 1      | 0 ~ 1     | -
70      | 2            | Raw reading FL                             | RAM    | R      | -      | 0 ~ 2047  | 3.3V * reading / 2047
72      | 2            | Raw reading FR                             | RAM    | R      | -      | 0 ~ 2047  | 3.3V * reading / 2047
74      | 2            | Raw reading BL                             | RAM    | R      | -      | 0 ~ 2047  | 3.3V * reading / 2047
76      | 2            | Raw reading BR                             | RAM    | R      | -      | 0 ~ 2047  | 3.3V * reading / 2047
78      | 2            | LPF reading FL                             | RAM    | R      | -      | 0 ~ 2047  | 3.3V * reading / 2047
80      | 2            | LPF reading FR                             | RAM    | R      | -      | 0 ~ 2047  | 3.3V * reading / 2047
82      | 2            | LPF reading BL                             | RAM    | R      | -      | 0 ~ 2047  | 3.3V * reading / 2047
84      | 2            | LPF reading BR                             | RAM    | R      | -      | 0 ~ 2047  | 3.3V * reading / 2047
85      | 1            | Voltage                                    | RAM    | R      | -      | 0 ~ 255   | in units of 0.1V
86      | 1            | Current                                    | RAM    | R      | -      | 0 ~ 255   | in units of 0.01A
87      | 1            | LPF Voltage                                | RAM    | R      | -      | 0 ~ 255   | in units of 0.1V
88      | 1            | LPF Current                                | RAM    | R      | -      | 0 ~ 255   | in units of 0.01A
98      | 2            | Loop rate                                  | RAM    | R      | -      | 0 ~ 65535 | in Hz
100     | 2            | Calibrated reading FL                      | RAM    | R      | -      | 0 ~ 2047  | 3.3V * reading / 2047
102     | 2            | Calibrated reading FR                      | RAM    | R      | -      | 0 ~ 2047  | 3.3V * reading / 2047
104     | 2            | Calibrated reading BL                      | RAM    | R      | -      | 0 ~ 2047  | 3.3V * reading / 2047
106     | 2            | Calibrated reading BR                      | RAM    | R      | -      | 0 ~ 2047  | 3.3V * reading / 2047

## Registers details

### Baud rate

Baud rate is handled as newer Dynamixel devices. The register contains a code that maps to actual baud rates as follows:


Value       | Baud Rate     | Margin of Error
------------|---------------|----------------
7           | 4.5M [bps]    | 0.000 [%]
6           | 4M [bps]      | 0.000 [%]
5           | 3M [bps]      | 0.000 [%]
4           | 2M [bps]      | 0.000 [%]
3 (Default) | 1M [bps]      | 0.000 [%]
2           | 115,200 [bps] | 0.000 [%]
1           |57,600 [bps]   | 0.000 [%]
0           | 9,600 [bps]   | 0.000 [%]

### Protocol version

SPR2010 supports both [Protocol 1](https://emanual.robotis.com/docs/en/dxl/protocol1/) and [Protocol 2](https://emanual.robotis.com/docs/en/dxl/protocol2/) Dynamixel communications.

### Low pass filter scalers

When reading analog signals with an ADC they will contain significant noise. One simple option to smoothen these readings is to subject them to a **low pass filter** (LPF). The common approach to implement such a filter is the following:

![\Large x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}](https://latex.codecogs.com/svg.latex?\Large&space;x=\frac{-b\pm\sqrt{b^2-4ac}}{2a})
