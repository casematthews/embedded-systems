# Arduino I²C Bus Scanner

An Arduino sketch that scans the I²C bus and reports the address of every device that responds. It's useful for checking wiring and for finding a sensor's address when the datasheet or breakout board doesn't make it clear.

## What it does

1. Starts the I²C bus (`Wire.begin()`) and opens the serial port at **9600 baud**.
2. Steps through every 7-bit address from **8 (0x08) to 119 (0x77)**. That's the full range available to normal devices; addresses outside it are reserved by the I²C spec.
3. For each address, calls the AVR TWI driver's `twi_writeTo()` with zero data bytes. That sends just the address and the write bit, then checks for an acknowledge (ACK):
   - return code `0` → a device ACKed → printed as `found!`
   - anything else → no device at that address
4. Prints the results to the Serial Monitor, four addresses per line.
5. When the scan is done, blinks the built-in LED on **pin 13** (300 ms on / 300 ms off) to show it finished.

The scan logic lives in `scanI2CBus()`, which takes a start address, an end address, and a callback function. The callback (`scanFunc()` here) receives each address and its result, so you can change how results are reported without touching the scan loop.

## Hardware

- An AVR-based Arduino (Uno, Nano, Mega, etc.). The sketch includes `utility/twi.h` from the AVR Wire library, so it's specific to AVR boards.
- One or more I²C devices wired to the board:

| Signal | Uno / Nano | Mega 2560 |
|--------|-----------|-----------|
| SDA    | A4        | 20        |
| SCL    | A5        | 21        |
| GND    | GND       | GND       |
| VCC    | 5V or 3.3V (per the device) | 5V or 3.3V (per the device) |

The I²C lines need pull-up resistors. Most breakout boards already have them; on a bare chip, add about 4.7 kΩ from SDA and from SCL to VCC.

## Usage

1. Open `Arduino_I2C_Bus_Scanner.ino` in the Arduino IDE.
2. Select your board and port, then upload.
3. Open the Serial Monitor at **9600 baud**.
4. Addresses that responded are marked `found!`. Once the scan prints `done`, the LED on pin 13 starts blinking.

To scan a different range, change `start_address` and `end_address` near the top of the sketch.
