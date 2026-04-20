# Raspberry Pi Pico + MicroPython — Extended Offline Cheat Sheet (Beginner → Builder)

Backup of a **single, offline, copy‑paste friendly** Markdown guide for the Raspberry Pi Pico / Pico W with MicroPython: GPIO, breadboarding, sensors, comms (UART/SPI/I2C), timing, interrupts, storage/logging, Pico W Wi‑Fi/web, plus optional Meshtastic maker workflows.

> **Audience:** myself so --> novice → intermediate  
> **Style:** “cheat sheet + why it works” (code snippets are commented with *what* and *why*).  
> **Use:** keep this file on an offline laptop in the garage and use Ctrl+F when stuck.

---

## Table of Contents

- [0. Quick Start (10 minutes)](#0-quick-start-10-minutes)
- [1. Microcontroller Basics](#1-microcontroller-basics)
  - [1.1 Microcontroller vs Computer](#11-microcontroller-vs-computer)
  - [1.2 Pico Variants](#12-pico-variants)
  - [1.3 Pin Naming (GP vs Physical Pin)](#13-pin-naming-gp-vs-physical-pin)
- [2. Safety + Power (Don’t Fry Stuff)](#2-safety--power-dont-fry-stuff)
  - [2.1 Golden Rules](#21-golden-rules)
  - [2.2 Power Pins Explained](#22-power-pins-explained)
  - [2.3 Servos/Motors (Brownouts + Separate Supply)](#23-servosmotors-brownouts--separate-supply)
- [3. Setup: Thonny + MicroPython + Workflow](#3-setup-thonny--micropython--workflow)
  - [3.1 Flash MicroPython UF2](#31-flash-micropython-uf2)
  - [3.2 Thonny Configuration](#32-thonny-configuration)
  - [3.3 Files on the Pico (main.py)](#33-files-on-the-pico-mainpy)
- [4. Electronics “Enough to Build Stuff”](#4-electronics-enough-to-build-stuff)
  - [4.1 Breadboard Basics](#41-breadboard-basics)
  - [4.2 Resistors for LEDs](#42-resistors-for-leds)
  - [4.3 Pull-up / Pull-down (Why Inputs Float)](#43-pull-up--pull-down-why-inputs-float)
- [5. MicroPython Essentials (Syntax + Patterns)](#5-micropython-essentials-syntax--patterns)
  - [5.1 Imports](#51-imports)
  - [5.2 Variables + Types](#52-variables--types)
  - [5.3 Conditionals](#53-conditionals)
  - [5.4 Loops](#54-loops)
  - [5.5 Functions + Scope](#55-functions--scope)
  - [5.6 Debugging Habits](#56-debugging-habits)
- [6. GPIO: Digital I/O](#6-gpio-digital-io)
  - [6.1 Output (Blink)](#61-output-blink)
  - [6.2 Input (Button)](#62-input-button)
  - [6.3 Debouncing Buttons](#63-debouncing-buttons)
- [7. Analog + PWM](#7-analog--pwm)
  - [7.1 ADC Read (0..65535)](#71-adc-read-065535)
  - [7.2 Convert ADC to Volts](#72-convert-adc-to-volts)
  - [7.3 PWM Dimming an LED](#73-pwm-dimming-an-led)
  - [7.4 PWM for Servo (Concept)](#74-pwm-for-servo-concept)
- [8. Timing (The Maker Superpower)](#8-timing-the-maker-superpower)
  - [8.1 Blocking vs Non-Blocking](#81-blocking-vs-non-blocking)
  - [8.2 ticks_ms() “Blink without delay”](#82-ticks_ms-blink-without-delay)
- [9. Interrupts + Timers](#9-interrupts--timers)
  - [9.1 GPIO Interrupt (IRQ)](#91-gpio-interrupt-irq)
  - [9.2 Timer Callback](#92-timer-callback)
- [10. Communication Protocols](#10-communication-protocols)
  - [10.1 UART](#101-uart)
  - [10.2 SPI](#102-spi)
  - [10.3 I2C](#103-i2c)
- [11. Sensors + Modules (How to Think)](#11-sensors--modules-how-to-think)
  - [11.1 Analog Sensors](#111-analog-sensors)
  - [11.2 Digital Sensors](#112-digital-sensors)
  - [11.3 I2C Address Conflicts](#113-i2c-address-conflicts)
  - [11.4 Filtering Noisy Data](#114-filtering-noisy-data)
- [12. Storage + Logging](#12-storage--logging)
  - [12.1 Internal Flash Files](#121-internal-flash-files)
  - [12.2 SD Card (SPI)](#122-sd-card-spi)
- [13. Pico W Wireless + Web](#13-pico-w-wireless--web)
  - [13.1 Connect to Wi‑Fi + HTTP GET](#131-connect-to-wi-fi--http-get)
  - [13.2 Minimal Web Server (LAN/AP)](#132-minimal-web-server-lanap)
  - [13.3 Web UI Template (Buttons + Slider + Telemetry)](#133-web-ui-template-buttons--slider--telemetry)
- [14. Libraries (Installing + Vendoring Offline)](#14-libraries-installing--vendoring-offline)
- [15. Project Checklists](#15-project-checklists)
  - [15.1 Bring-up Checklist](#151-bring-up-checklist)
  - [15.2 Debug Checklist](#152-debug-checklist)
  - [15.3 New Project Skeleton](#153-new-project-skeleton)
- [16. Optional: Meshtastic for Makers (Pico + LoRa Module)](#16-optional-meshtastic-for-makers-pico--lora-module)
  - [16.1 What Meshtastic Is (Builder View)](#161-what-meshtastic-is-builder-view)
  - [16.2 Hardware + RF Warning](#162-hardware--rf-warning)
  - [16.3 Flashing Firmware (UF2)](#163-flashing-firmware-uf2)
  - [16.4 Region + Frequency Slot + Channels](#164-region--frequency-slot--channels)
  - [16.5 UART Bridge Pattern (2-MCU)](#165-uart-bridge-pattern-2-mcu)
  - [16.6 Send Sensor Data (UART → Mesh)](#166-send-sensor-data-uart--mesh)
  - [16.7 Receive + Control Hardware](#167-receive--control-hardware)
  - [16.8 OLED Dashboard Pattern](#168-oled-dashboard-pattern)
  - [16.9 MQTT Bridge Concept (Internet ↔ Mesh)](#169-mqtt-bridge-concept-internet--mesh)
  - [16.10 “No Second MCU” Detection Module Idea](#1610-no-second-mcu-detection-module-idea)
- [17. Quick Reference Snippets](#17-quick-reference-snippets)
- [18. Offline Links to Save](#18-offline-links-to-save)

---

## 0. Quick Start (10 minutes)

### Minimal parts
- Raspberry Pi Pico (or Pico W)
- Micro‑USB cable (data capable)
- Breadboard + jumper wires
- LED + resistor (220Ω–1kΩ)
- Button (optional but very helpful)

### Steps
1. Install **Thonny** on laptop
2. Flash **MicroPython UF2** to the Pico (once)
3. Thonny → select **MicroPython (Raspberry Pi Pico)** interpreter
4. Run the blink code in [6.1](#61-output-blink)

---

## 1. Microcontroller Basics

### 1.1 Microcontroller vs Computer

A Pico is a **microcontroller board**:
- runs one main program (script/firmware)
- directly controls pins (hardware)
- low power, no full desktop OS
- great for sensors, motors, LEDs, displays, simple networking (Pico W)

### 1.2 Pico Variants
- **Pico**: base RP2040 microcontroller
- **Pico W**: adds Wi‑Fi + Bluetooth hardware (Wi‑Fi commonly used)
- **Pico H / WH**: same but with pre-soldered headers

### 1.3 Pin Naming (GP vs Physical Pin)

- In code you usually refer to **GPIO numbers**: `GP0`, `GP1`, … (MicroPython often uses just numbers: `Pin(16)` means `GP16`)
- On the physical board there are **physical pin positions**—don’t confuse these with GPIO numbers.
- **Tip:** Keep a printed Pico pinout at the bench.

1.4 ESP32 vs. RP2040 (Pico) — Key Differences
If you are switching from Pico to ESP32 (or using both), note these critical differences:

Architecture: ESP32 is dual-core (can run two tasks simultaneously); Pico is single-core (though it has PIO for offloading).
Connectivity: ESP32 has built-in Wi-Fi & Bluetooth. Pico requires the "W" variant for Wi-Fi and has no native BT.
Voltage: Both are 3.3V logic. Warning: Some ESP32 GPIOs are 5V tolerant (check specific model), but Pico GPIOs are NOT 5V tolerant.
Pin Flexibility: ESP32 pins are highly multiplexed (any pin can often be I2C, SPI, or UART). Pico pins are more rigidly assigned to specific peripherals.
Power Management: ESP32 has a dedicated Deep Sleep mode (ultra-low power, wakes on timer/GPIO). Pico has a "sleep" mode but it behaves differently and consumes more power than ESP32 deep sleep.


---

## 2. Safety + Power (Don’t Fry Stuff)

### 2.1 Golden Rules
✅ Do:
- **Unplug USB** before changing wiring (avoid accidental shorts)
- Verify wiring twice (especially power rails)
- Always share **GND** between Pico and external devices/supplies
- Use resistors with LEDs

❌ Don’t:
- Feed **5V into GPIO pins** (RP2040 GPIO is **3.3V logic**, not 5V tolerant)
- Short `3V3` to `GND`
- Power motors/servos from `3V3_OUT`
- “Guess” with power—measure with multimeter if unsure

If the Pico becomes hot quickly → unplug immediately.

### 2.2 Power Pins Explained
- **VBUS**: ~5V from USB (limited by  USB power source)
- **VSYS**: main input for external supply (often 1.8–5.5V input range)
- **3V3_OUT**: regulated 3.3V output for sensors (limited current)
- **GPIO**: 0–3.3V output signals, low current only (LEDs, logic)

⚠️ Avoid powering via **USB and VSYS simultaneously** unless you implement proper power OR-ing/protection.

### 2.3 Servos/Motors (Brownouts + Separate Supply)
Servos and motors draw **current spikes**. That can:
- reset the Pico (brownout)
- make ADC readings noisy
- cause “random” glitches

**Best practice**
- Use a separate 5V supply for servo/motor power
- Connect the external supply **GND** to Pico **GND** (common reference)
- Use a driver (MOSFET, H‑bridge, motor driver board) for motors

---

## 3. Setup: Thonny + MicroPython + Workflow

### 3.1 Flash MicroPython UF2
1. Hold **BOOTSEL**
2. Plug the Pico into USB
3. A USB drive appears
4. Copy MicroPython `.uf2` to it
5. Pico reboots into MicroPython

### 3.2 Thonny Configuration
- Tools → Options → Interpreter:
  - **MicroPython (Raspberry Pi Pico)**
- Use the Thonny shell as  REPL.

### 3.3 Files on the Pico (main.py)
- `main.py` runs automatically at boot
- `boot.py` runs early (rare)
- `lib/` folder holds external libraries you copy onto the Pico

---

## 4. Electronics “Enough to Build Stuff”

### 4.1 Breadboard Basics
- Rows are connected in groups (usually 5 holes per row)
- Side rails are often used for + and GND
- Always confirm rail continuity (some breadboards split rails in the middle)

### 4.2 Resistors for LEDs
LEDs need current limiting:
- 220Ω is a common default for 3.3V logic
- 330Ω–1kΩ works too (dim but safe)

### 4.3 Pull-up / Pull-down (Why Inputs Float)
An input pin without a defined voltage can “float” and randomly read 0/1.
Fix it using:
- internal pull-up/down resistors (`Pin.PULL_UP`, `Pin.PULL_DOWN`)
- or external resistors

---

## 5. MicroPython Essentials (Syntax + Patterns)

### 5.1 Imports
```python
# machine = hardware access
from machine import Pin, ADC, PWM, I2C, SPI, UART
import time  # timing, sleeps, ticks
```

### 5.2 Variables + Types
```python
# Integers
count = 0

# Floats (decimal values)
voltage = 3.3

# Strings (text)
name = "Pico"

# Booleans
enabled = True

# Lists
values = [1, 2, 3]
```

### 5.3 Conditionals
```python
x = 10

# Compare and branch based on the result
if x > 10:
    print("big")
elif x == 10:
    print("exactly 10")
else:
    print("small")
```

### 5.4 Loops
```python
# Known number of iterations
for i in range(5):
    print(i)

# Loop forever (common in embedded)
while True:
    # do something repeatedly
    pass
```

### 5.5 Functions + Scope
```python
total = 0  # global variable

def add_one():
    # We declare global because we're modifying the global variable.
    # Without "global", Python would create a new local variable named total.
    global total
    total += 1
```

### 5.6 Debugging Habits
- Add `print()` checkpoints: “where did it stop?”
- Wrap risky code in `try/except`
- Reduce to the simplest example (blink) when stuck

```python
try:
    # risky call (sensor/network)
    val = 1 / 0
except Exception as e:
    print("Something failed:", e)
```

---

## 6. GPIO: Digital I/O

### 6.1 Output (Blink)

#### Onboard LED blink
```python
from machine import Pin
import time

# "LED" is a special name for the onboard LED on the Pico.
# Using Pin.OUT configures it as an output (we control it).
led = Pin("LED", Pin.OUT)

while True:
    # value(1) drives the pin HIGH (3.3V) -> LED on
    led.value(1)
    time.sleep(0.5)  # wait (blocks the CPU)
    # value(0) drives pin LOW (0V) -> LED off
    led.value(0)
    time.sleep(0.5)
```

#### External LED (example GP16)
Wiring:
- GP16 → resistor → LED → GND

```python
from machine import Pin
import time

led = Pin(16, Pin.OUT)  # GP16

while True:
    # toggle flips between 0 and 1 (off/on) -> convenient for blink
    led.toggle()
    time.sleep(0.2)
```

### 6.2 Input (Button)

Example using **internal pull-down**:
- Button connects GP15 to 3.3V when pressed
- Pull-down keeps it at 0 when released

```python
from machine import Pin
import time

# Pin.IN means the Pico reads this pin
# Pin.PULL_DOWN activates an internal resistor to ground
button = Pin(15, Pin.IN, Pin.PULL_DOWN)

while True:
    # value() reads 0 or 1
    print(button.value())
    time.sleep(0.1)
```

Button controls LED:
```python
from machine import Pin
import time

button = Pin(15, Pin.IN, Pin.PULL_DOWN)
led = Pin(16, Pin.OUT)

while True:
    # We directly set LED state to button state:
    # - button pressed -> 1 -> LED on
    # - released -> 0 -> LED off
    led.value(button.value())
    time.sleep(0.01)
```

### 6.3 Debouncing Buttons

Buttons “bounce” (rapid transitions) for a few milliseconds.
Without debounce you may register one press as many presses.

```python
from machine import Pin
import time

button = Pin(15, Pin.IN, Pin.PULL_DOWN)

last_press_ms = 0
debounce_ms = 50  # ignore new presses within 50ms

while True:
    now = time.ticks_ms()

    # If button is pressed (reads 1) AND enough time passed since last press:
    if button.value() == 1 and time.ticks_diff(now, last_press_ms) > debounce_ms:
        last_press_ms = now  # remember the press time
        print("Pressed once (debounced)!")

    time.sleep_ms(5)  # small delay to reduce CPU usage
```

---

## 7. Analog + PWM

### 7.1 ADC Read (0..65535)

ADC pins are commonly on GPIO 26–28.
```python
from machine import ADC
import time

# ADC(26) uses ADC0 on GPIO26
pot = ADC(26)

while True:
    raw = pot.read_u16()  # 0..65535 (16-bit)
    print("RAW:", raw)
    time.sleep(0.1)
```

### 7.2 Convert ADC to Volts

Why conversion?
- The ADC gives a number 0..65535, not volts.
- We convert to volts using (3.3V / 65535).

```python
from machine import ADC
import time

pot = ADC(26)
conv = 3.3 / 65535  # scale factor

while True:
    raw = pot.read_u16()
    volts = raw * conv  # approximate voltage on the pin
    print("RAW:", raw, "V:", volts)
    time.sleep(0.1)
```

### 7.3 PWM Dimming an LED

Why PWM?
- Pico can’t output “true analog voltage”
- PWM toggles the pin very fast; the LED averages it as brightness.

```python
from machine import Pin, PWM
import time

# Create PWM object on GP16
pwm = PWM(Pin(16))

# Set PWM frequency (how fast it toggles). 1000 Hz is good for LED dimming.
pwm.freq(1000)

while True:
    # duty_u16 sets duty cycle: 0..65535
    # bigger -> more ON time -> brighter LED
    pwm.duty_u16(5000)
    time.sleep(1)

    pwm.duty_u16(50000)
    time.sleep(1)
```

Pot controls brightness:
```python
from machine import Pin, PWM, ADC
import time

pot = ADC(26)
pwm = PWM(Pin(16))
pwm.freq(1000)

while True:
    # Use the ADC value directly as PWM duty.
    # WHY: both ranges are 0..65535 so it's a perfect mapping.
    pwm.duty_u16(pot.read_u16())
    time.sleep(0.01)
```

### 7.4 PWM for Servo (Concept)

Servos usually want:
- **50 Hz** PWM frequency
- pulse width ~1ms..2ms within the 20ms frame

Use a library if possible to avoid calibration pain.

---

## 8. Timing (The Maker Superpower)

### 8.1 Blocking vs Non-Blocking

- `sleep()` blocks: nothing else runs.
- non-blocking timing uses `ticks_ms()` so  code can do multiple tasks “at once”.

### 8.2 ticks_ms() “Blink without delay”

```python
from machine import Pin
import time

led = Pin("LED", Pin.OUT)

interval_ms = 500

# next_toggle_ms is the future time when we want to toggle again
next_toggle_ms = time.ticks_add(time.ticks_ms(), interval_ms)

while True:
    now = time.ticks_ms()

    # ticks_diff handles overflow correctly (ticks wrap around eventually).
    if time.ticks_diff(now, next_toggle_ms) >= 0:
        led.toggle()
        # schedule the next toggle based on current time
        next_toggle_ms = time.ticks_add(now, interval_ms)

    # Meanwhile we can do other tasks here without blocking:
    # - read sensors
    # - update a display
    # - check UART messages
    # (just keep those tasks short)
```

---

## 9. Interrupts + Timers

### 9.1 GPIO Interrupt (IRQ)

Why IRQ?
- Instead of constantly polling a button, an interrupt triggers  handler on an edge.

⚠️ Keep interrupt handlers short.
- Set flags
- Don’t do long work
- Avoid heavy prints/allocations inside ISR

```python
from machine import Pin
import time

button = Pin(15, Pin.IN, Pin.PULL_DOWN)

pressed_flag = False

def on_press(pin):
    # This function runs when the interrupt triggers.
    # Keep it short: just set a flag.
    global pressed_flag
    pressed_flag = True

# Trigger on rising edge (0 -> 1) i.e., button press
button.irq(trigger=Pin.IRQ_RISING, handler=on_press)

while True:
    if pressed_flag:
        pressed_flag = False
        print("Button pressed (IRQ)!")

    time.sleep_ms(10)
```

### 9.2 Timer Callback

Why timers?
- Run something periodically without writing  own tick logic.

```python
from machine import Timer
import time

tick_flag = False

def timer_cb(t):
    global tick_flag
    tick_flag = True  # again: keep callbacks short

tim = Timer()
# freq=2 means callback runs 2 times per second
tim.init(freq=2, mode=Timer.PERIODIC, callback=timer_cb)

while True:
    if tick_flag:
        tick_flag = False
        print("Timer tick")

    time.sleep_ms(10)
```

---

## 10. Communication Protocols

## 10.1 UART

UART basics:
- Two wires + ground:
  - TX (send) from device A → RX on device B
  - RX on device A ← TX on device B
- Must share GND
- Send text (bytes/strings)

Sender:
```python
from machine import UART, Pin
import time

# UART(1) is a hardware UART peripheral.
# Choose pins that support UART (many do).
uart = UART(1, baudrate=9600, tx=Pin(4), rx=Pin(5))

while True:
    # UART sends bytes. MicroPython accepts str and encodes as bytes.
    uart.write("HELLO\n")
    time.sleep(1)
```

Receiver:
```python
from machine import UART, Pin
import time

uart = UART(1, baudrate=9600, tx=Pin(4), rx=Pin(5))

while True:
    # any() tells you if bytes are waiting in the RX buffer
    if uart.any():
        data = uart.read()  # reads all available bytes
        # decode bytes -> text for printing
        text = data.decode("utf-8", errors="ignore").strip()
        print("Got:", text)

    time.sleep_ms(10)
```

**Common UART mistake:** forgetting to cross TX/RX.

---

## 10.2 SPI

SPI uses:
- SCK, MOSI, MISO, CS (chip select)
- One controller (Pico) talks to many targets using different CS pins.

Skeleton:
```python
from machine import SPI, Pin

# Initialize SPI bus
spi = SPI(1, sck=Pin(14), mosi=Pin(15), miso=Pin(12))

# Chip select pin controls which device is active
cs = Pin(13, Pin.OUT)
cs.value(1)  # deselect by default

# Actual usage depends heavily on the device library/driver.
```

---

## 10.3 I2C

I2C uses:
- SDA (data), SCL (clock)
- Devices have addresses; Pico uses that to talk to each device.

Scanner (first thing you do when an I2C device “doesn’t work”):
```python
from machine import Pin, I2C

i2c = I2C(0, scl=Pin(9), sda=Pin(8), freq=400000)

# scan() returns a list of device addresses found on the bus
addrs = i2c.scan()
print("Found:", [hex(a) for a in addrs])
```

SSD1306 OLED example:
```python
from machine import Pin, I2C
import ssd1306

# Setup I2C bus
i2c = I2C(0, scl=Pin(9), sda=Pin(8), freq=400000)

# Create OLED driver instance
oled = ssd1306.SSD1306_I2C(128, 64, i2c, addr=0x3c)

oled.fill(0)               # clear buffer
oled.text("Hello Pico", 0, 0)
oled.show()                # push buffer to screen
```

---

## 11. Sensors + Modules (How to Think)

### 11.1 Analog Sensors
- output a voltage (0..3.3V)
- read with ADC
- noisy → filter

### 11.2 Digital Sensors
Two categories:
1) **Simple digital**: HIGH/LOW (PIR, reed switch)
2) **Protocol digital**: I2C/SPI/UART sensors (BME280, OLEDs, GPS)

### 11.3 I2C Address Conflicts
- On one bus, each address must be unique.
- Two identical sensors often share the same factory address.
- Solutions:
  - change address via solder pads/jumpers
  - use a second I2C bus
  - use an I2C multiplexer

### 11.4 Filtering Noisy Data

**Moving average** (smooth but adds lag):
```python
def moving_average(buf):
    return sum(buf) / len(buf)

window = [0, 0, 0, 0, 0]
idx = 0

while True:
    # Put new value in ring buffer
    window[idx] = pot.read_u16()
    idx = (idx + 1) % len(window)

    print("Smoothed:", moving_average(window))
```

**Threshold update** (ignore tiny changes):
```python
last = 0
threshold = 2000  # tune this

while True:
    val = pot.read_u16()
    if abs(val - last) > threshold:
        last = val
        print("Updated:", val)
```

---

## 12. Storage + Logging

### 12.1 Internal Flash Files
Good for configs and small logs.

```python
# Append a line to a file
with open("log.txt", "a") as f:
    f.write("booted\n")
```

### 12.2 SD Card (SPI)
You typically need an `sdcard.py` library.

```python
from machine import SPI, Pin
import uos
import sdcard
import time

# Setup SPI bus and chip select pin
spi = SPI(1, sck=Pin(14), mosi=Pin(15), miso=Pin(12))
cs = Pin(13)

# Create SD card driver instance
sd = sdcard.SDCard(spi, cs)

# Mount the SD card filesystem so it appears at /sd
uos.mount(sd, "/sd")
print("SD contents:", uos.listdir("/sd"))

# Write data to a file
with open("/sd/data.txt", "a") as f:
    for i in range(5):
        f.write(f"{i}\n")
        f.flush()  # WHY: forces buffered data to actually be written
        time.sleep(0.2)
```

---

## 13. Pico W Wireless + Web

### 13.1 Connect to Wi‑Fi + HTTP GET
```python
import network
import time
import urequests

SSID = "your_wifi"
PW = "your_pass"

def connect_wifi():
    # STA_IF = station mode (connect to an existing Wi‑Fi router)
    wlan = network.WLAN(network.STA_IF)
    wlan.active(True)
    wlan.connect(SSID, PW)

    # Wait until connected
    while not wlan.isconnected():
        print("Connecting...")
        time.sleep(1)

    print("Connected. IP:", wlan.ifconfig()[0])
    return wlan

try:
    connect_wifi()

    # Query a URL that returns JSON
    r = urequests.get("http://worldtimeapi.org/api/ip")
    print(r.json())
    r.close()

except Exception as e:
    # WHY: network code fails often during setup; don’t crash silently
    print("Network error:", e)
```

### 13.2 Minimal Web Server (LAN/AP)

```python
import network
import socket
import time
from machine import Pin

led = Pin("LED", Pin.OUT)

SSID = "pico-ap"
PW = "pico-password"

def start_ap():
    # AP_IF = access point mode (Pico hosts Wi‑Fi)
    ap = network.WLAN(network.AP_IF)
    ap.config(ssid=SSID, password=PW)
    ap.active(True)
    while not ap.active():
        time.sleep(0.5)
    print("AP IP:", ap.ifconfig()[0])  # usually 192.168.4.1

def webpage(state):
    # Minimal HTML; browsers will display this string as a webpage
    return f'<!doctype html\n<html>\n  <body>\n    <h2>Pico W</h2>\n    <p>LED: {state}</p>\n    <p><a href="/on">ON</a> | <a href="/off">OFF</a></p>\n  </body>\n</html>\n'

start_ap()

# Bind a socket on port 80 (HTTP)
addr = socket.getaddrinfo("0.0.0.0", 80)[0][-1]
s = socket.socket()
s.bind(addr)
s.listen(1)
print("Listening on", addr)

state = "OFF"

while True:
    client, remote = s.accept()

    # Read request (browser sends HTTP request text)
    req = client.recv(1024).decode(errors="ignore")

    # Extract path from the first request line: "GET /on HTTP/1.1"
    try:
        path = req.split(" ")[1]
    except:
        path = "/"

    if path == "/on":
        led.value(1)
        state = "ON"
    elif path == "/off":
        led.value(0)
        state = "OFF"

    html = webpage(state)

    # Send HTTP response header + content
    client.send("HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n")
    client.send(html)
    client.close()
```

### 13.3 Web UI Template (Buttons + Slider + Telemetry)

```python
import network, socket, time
from machine import Pin, ADC, PWM

led = Pin("LED", Pin.OUT)
temp_adc = ADC(4)  # onboard temperature sensor channel
pwm = PWM(Pin(16))
pwm.freq(1000)

SSID = "pico-ap"
PW = "pico-password"

def read_temp_c():
    # Convert ADC reading into temperature (approx)
    # WHY: ADC gives a number; formula converts to °C for RP2040 sensor
    conv = 3.3 / 65535
    v = temp_adc.read_u16() * conv
    return 27 - (v - 0.706) / 0.001721

def start_ap():
    ap = network.WLAN(network.AP_IF)
    ap.config(ssid=SSID, password=PW)
    ap.active(True)
    while not ap.active():
        time.sleep(0.5)
    print("AP IP:", ap.ifconfig()[0])

def parse_request(req_text):
    # Extract the first line and get the URL part
    # Example: "GET /pwm?v=42 HTTP/1.1"
    try:
        first_line = req_text.split("\r\n")[0]
        target = first_line.split(" ")[1]
    except:
        return "/", ""

    if "?" in target:
        path, query = target.split("?", 1)
    else:
        path, query = target, ""
    return path, query

def get_query_param(query, key, default=None):
    # Very small query parser (a=1&b=2)
    for part in query.split("&"):
        if "=" in part:
            k, v = part.split("=", 1)
            if k == key:
                return v
    return default

def webpage(state, temp_c, slider):
    # Simple HTML form for slider and buttons.
    return f'''<!doctype html>
<html>
  <head><meta name="viewport" content="width=device-width, initial-scale=1"/></head>
  <body>
    <h2>Pico W Control Panel</h2>

    <p><b>LED:</b> {state}</p>
    <p><b>Temp:</b> {temp_c:.2f} °C</p>

    <form action="/on"><button>LED ON</button></form>
    <form action="/off"><button>LED OFF</button></form>

    <hr/>
    <p><b>PWM (GP16):</b> {slider}%</p>
    <form action="/pwm">
      <input type="range" name="v" min="0" max="100" value="{slider}"
             oninput="this.nextElementSibling.value=this.value">
      <output>{slider}</output>
      <button type="submit">Set PWM</button>
    </form>
  </body>
</html>'''

start_ap()

addr = socket.getaddrinfo("0.0.0.0", 80)[0][-1]
s = socket.socket()
s.bind(addr)
s.listen(1)

state = "OFF"
slider = 0

while True:
    cl, _ = s.accept()
    req = cl.recv(1024).decode(errors="ignore")

    path, query = parse_request(req)

    if path == "/on":
        led.value(1)
        state = "ON"

    elif path == "/off":
        led.value(0)
        state = "OFF"

    elif path == "/pwm":
        v = get_query_param(query, "v", "0")
        try:
            # Clamp to 0..100
            slider = max(0, min(100, int(v)))

            # Convert percent to duty 0..65535
            duty = int(slider * 65535 / 100)
            pwm.duty_u16(duty)
        except:
            pass

    temp_c = read_temp_c()
    html = webpage(state, temp_c, slider)

    cl.send("HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n")
    cl.send(html)
    cl.close()
```

---

## 14. Libraries (Installing + Vendoring Offline)

### Installing via Thonny (online once)
Tools → Manage Packages:
- `micropython-servo`
- `micropython-ssd1306-driver`
- `micropython-umqtt.simple`

### Vendoring offline (recommended)
- Copy `.py` library files into `lib/` on the Pico
- Then import normally (e.g. `import ssd1306`)

Common errors:
- `ImportError`: library not on Pico or wrong filename
- `MemoryError`: library too large; remove features or choose smaller libs

---

## 15. Project Checklists

### 15.1 Bring-up Checklist
- [ ] Pico unplugged while wiring
- [ ] LED has resistor
- [ ] External power shares GND with Pico
- [ ] No 5V into GPIO
- [ ] Correct GPIO numbers used in code
- [ ] For I2C: scan shows the address you expect

### 15.2 Debug Checklist
- [ ] Add print checkpoints
- [ ] Read error messages in Thonny shell
- [ ] Simplify to blink test
- [ ] Check jumpers (bad wires are common)
- [ ] Check input pull-up/down
- [ ] Check power stability (servo/motor issues)

### 15.3 New Project Skeleton
```python
# main.py
import time

def setup():
    # Initialize hardware here
    pass

def loop():
    # One iteration of logic
    pass

def main():
    setup()
    while True:
        loop()
        time.sleep_ms(10)  # small delay to reduce CPU usage

main()
```

---

## 16. Optional: Meshtastic for Makers (Pico + LoRa Module)

### 16.1 What Meshtastic Is (Builder View)
- Firmware that runs on supported devices
- Uses LoRa for long-range, low-power messaging
- Mesh: devices can repeat messages to extend range
- Messages can be encrypted (repeaters don’t need to read them)

### 16.2 Hardware + RF Warning
Typical:
- Pico (or Pico W)
- LoRa module + antenna

⚠️ **Never power the LoRa module without an antenna**.

### 16.3 Flashing Firmware (UF2)
- BOOTSEL + plug in
- copy Meshtastic UF2
- reboot

### 16.4 Region + Frequency Slot + Channels
- Set **region** correctly (legal frequencies differ by country)
- Frequency slot isolates networks
- Channels are like group chats (use private for project telemetry)

### 16.5 UART Bridge Pattern (2-MCU)
- One device runs Meshtastic firmware
- Second microcontroller reads sensors and sends UART text
- Meshtastic sends that text over the mesh

### 16.6 Send Sensor Data (UART → Mesh)
```python
from machine import UART, Pin, ADC
import time

uart = UART(1, baudrate=115200, tx=Pin(8), rx=Pin(9))
soil = ADC(26)

while True:
    reading = soil.read_u16()
    uart.write("soil1:" + str(reading) + "\n")
    time.sleep(60)
```

### 16.7 Receive + Control Hardware
```python
from machine import UART, Pin
import time
from servo import Servo

uart = UART(1, baudrate=115200, tx=Pin(8), rx=Pin(9))
servo = Servo(pin_id=0)

def extract_text(raw_bytes):
    s = raw_bytes.decode("utf-8", errors="ignore")
    lines = [ln.strip() for ln in s.splitlines() if ln.strip()]
    return lines[-1] if lines else ""

while True:
    if uart.any():
        msg = extract_text(uart.read())
        if msg.startswith("servoA:"):
            try:
                angle = int(msg.split(":")[1])
                angle = max(0, min(180, angle))
                servo.write(angle)
            except:
                pass

    time.sleep_ms(50)
```

### 16.8 OLED Dashboard Pattern
- prefixes: `soil1:`, `temp:`, `door:`
- display last values + timestamps
- update “time ago” periodically

### 16.9 MQTT Bridge Concept (Internet ↔ Mesh)
- Wi‑Fi MCU connects to MQTT
- forwards mesh UART → MQTT publish
- forwards MQTT messages → UART → mesh

### 16.10 “No Second MCU” Detection Module Idea
If firmware supports detection module:
- monitor a pin
- send detection messages or periodic state

---

## 17. Quick Reference Snippets

### I2C scan
```python
from machine import I2C, Pin
i2c = I2C(0, scl=Pin(9), sda=Pin(8))
print([hex(a) for a in i2c.scan()])
```

### ADC to volts
```python
def adc_to_volts(raw):
    return raw * (3.3 / 65535)
```

### PWM from percent
```python
def duty_from_percent(pct):
    pct = max(0, min(100, int(pct)))
    return int(pct * 65535 / 100)
```

### Non-blocking periodic task
```python
import time
next_t = time.ticks_add(time.ticks_ms(), 1000)

while True:
    if time.ticks_diff(time.ticks_ms(), next_t) >= 0:
        next_t = time.ticks_add(next_t, 1000)
        print("1-second event")
```

---

19. ESP32 Deep Sleep & Power Management
The ESP32 excels at battery-powered projects thanks to Deep Sleep. In this mode, the main CPU and most peripherals are powered down, consuming only ~10µA.

Wake-Up Sources
You can wake the ESP32 via:

Timer: Wake after 
X
 seconds.
GPIO: Wake when a specific pin changes state (e.g., button press).
Touch Sensors: Wake when a capacitive touch threshold is crossed.

```
import machine
import time

# Configure Deep Sleep
# Wake up after 10 seconds (10,000,000 microseconds)
machine.deepsleep(10000000)

# Code below only runs AFTER waking up
print("ESP32 woke up!")

# Do your work here (read sensor, send data)
# ...

# Enter deep sleep again at the end of the script
machine.deepsleep(10000000)
```

```
import machine
import time

# Define the wake-up pin (e.g., GPIO 0)
# Note: GPIO 0 is often used for boot flashing, check your board!
wake_pin = machine.Pin(0, machine.Pin.IN)

# Configure wake-up: wake when pin goes HIGH (1)
# Options: machine.Pin.IRQ_RISING, machine.Pin.IRQ_FALLING
wake_pin.irq(trigger=machine.Pin.IRQ_RISING, handler=lambda p: None)

# Enable wake-up source
machine.enable_irq(wake_pin)

# Enter deep sleep
print("Going to sleep...")
machine.deepsleep()

# Code resumes here after button press
print("Woke up by button!")
```
so a simple flow in C is : 

```

void setup () {

# Do what needs to be done. Set wake-up source. Prep for sleep. Enable sleep
}
void loop ()  # This will not be used then

```

so basically TAKE THIS FOR DEEP SLEEP

```
void setup () {

esp_sleep_enable_timer_wakeup (1000000);

esp_deep_sleep_start();
}

void loop () 

```



---

### Notes for Future Self

- When in doubt: **test the simplest thing** (blink) and build up step-by-step.
- Most “mystery bugs” are:
  - wrong wiring
  - wrong pin number
  - floating input (no pull-up/down)
  - power instability (servo/motor)
  - missing library file

Good luck to myself --> and have fun!!
