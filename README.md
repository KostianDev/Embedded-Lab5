# Embedded Lab 5 - USART Remote LED Control

STM32F407 project implementing remote LED control via USART interface. PC sends commands using a serial terminal (PuTTY), STM32F407 receives and processes them to control on-board LEDs. Firmware is generated with STM32CubeMX and built with the supplied Makefile.

## Hardware
- **MCU**: `STM32F407VGT6`
- **USART Interface**: USART3 (PD8 - TX, PD9 - RX)
- **Serial Settings**: 19200 baud, 8 data bits, no parity, 1 stop bit, no flow control
- **USB-TTL Converter**: Connected to USART3 for PC communication
- **LEDs (GPIO outputs)**: 
  - PD12 - `LED_RED`
  - PD13 - `LED_YELLOW` (replaces orange)
  - PD14 - `LED_BLUE`
  - PD15 - `LED_GREEN`
- **Debug/programming**: SWD (ST-LINK)

## Мета роботи (Objective)
Understand how USART interface works and how to configure it. Implement remote LED control via serial communication in polling mode.

## What this lab does
- Receives single-byte commands over USART from a PC serial terminal
- Processes commands to toggle individual LEDs or control all LEDs
- Echoes received characters back to the terminal
- Sends status messages confirming executed commands
- Operates entirely in polling mode (no interrupts)

## Implementation notes
- **USART Configuration**: USART3 initialized via CubeMX with 19200 baud rate, 8N1 format, no hardware flow control
- **GPIO Setup**: All four LEDs configured as GPIO outputs (push-pull, low speed)
- **Polling Mode**: Uses `HAL_UART_Receive()` with 100ms timeout in the main loop instead of interrupt-driven reception
- **Command Processing**: Switch-case structure handles command characters (case-insensitive: G/g, Y/y, R/r, B/b, A/a, O/o)
- **Echo & Feedback**: Every received character is echoed back; status messages confirm LED state changes

## Commands
| Command | Action |
|---------|--------|
| `G` / `g` | Toggle GREEN LED |
| `Y` / `y` | Toggle YELLOW LED |
| `R` / `r` | Toggle RED LED |
| `B` / `b` | Toggle BLUE LED |
| `A` / `a` | Turn ON all LEDs |
| `O` / `o` | Turn OFF all LEDs |

## Serial Connection
**USB-TTL Converter pinout:**
- Converter **TX** -> Microcontroller **RX** (PD9)
- Converter **RX** -> Microcontroller **TX** (PD8)
- Converter **GND** -> Microcontroller **GND**
- Converter **VCC** -> 3.3V or 5V (check converter specs)

⚠️ **Important**: TX and RX are crossed! Converter TX connects to MCU RX and vice versa.

## PuTTY Configuration
1. **Connection type**: Serial
2. **Serial line**: COM port of your USB-TTL converter (check Device Manager)
3. **Speed**: 19200
4. **Data bits**: 8
5. **Stop bits**: 1
6. **Parity**: None
7. **Flow control**: None

After connecting, the application displays a command menu via USART.

## Build
Run in project root:
```bash
make
```
Artifacts are placed under `build/` (for example `build/Lab5.bin`, `build/Lab5.hex`).

## Flash
Use your preferred SWD programmer. Examples:

**STM32CubeProgrammer CLI:**
```bash
STM32_Programmer_CLI -c port=SWD -w build/Lab5.bin 0x08000000 -v -rst
```

**ST-LINK (stlink-tools):**
```bash
st-flash --reset write build/Lab5.bin 0x08000000
```