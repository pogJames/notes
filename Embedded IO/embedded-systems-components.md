# 1. Core Components

Most embedded projects follow a "Sense → Process → Act" loop. These are the components that make it happen:

### The Brain

1. MCU (Microcontroller Unit)
> A single small chip that contains CPU, RAM, flash memory, and lots of built-in peripherals (timers, ADC, PWM, UART, etc.) designed for real-time control, low power, and deterministic behavior.
2. MPU (Microprocessor Unit)
> Just a fast CPU core (plus cache) with no built-in memory or peripherals — it needs external RAM, flash, and support chips to become a working system (rarely used standalone today).
3. SoC (System on Chip)
> A highly integrated single chip that combines powerful application CPU(s), GPU, memory controller, video engines, high-speed interfaces (USB, PCIe, HDMI), and often some real-time cores — basically a complete powerful computer on one die.
4. SoM (System on Module)
> A small ready-made board that takes a difficult-to-use SoC (plus soldered RAM, flash, power management) and brings all the tricky high-speed signals out to one big connector so you can easily plug it into your own simpler carrier board.
5. SBC (Single Board Computer)
> A complete, ready-to-use computer board (usually based on an SoC) with all connectors (HDMI, USB, Ethernet, GPIO, etc.) already available — you just add power and it works like a tiny desktop (e.g. Raspberry Pi).

### The Nervous System: Communication Protocols

Components don't just "talk"; they follow strict rules. You only need to master three:

1. **UART:** Point-to-point (e.g., MCU to GPS or PC).
2. **I2C:** Multi-device, low speed (e.g., MCU to Temperature Sensor).
3. **SPI:** High speed, short distance (e.g., MCU to SD Card or Display).

### The Senses & Limbs: I/O Peripherals

* **GPIO (General Purpose I/O):** Simple High/Low signals to flip switches or read buttons.
* **ADC (Analog-to-Digital Converter):** Translates real-world voltages (like a sensor) into numbers the MCU understands.
* **PWM (Pulse Width Modulation):** Mimics "analog" output to dim LEDs or control motor speeds.

### Others

1. Power Management
> Voltage regulators, power supply circuits, and sometimes battery management systems. Many embedded systems run on 3.3V or 5V logic levels.
2. Sensors and Actuators
> Sensors gather environmental data (temperature, pressure, motion). Actuators control physical outputs (motors, relays, LEDs).
3. Clock/Oscillator
> Provides timing signals. Can be internal crystal oscillators or external clock sources, typically ranging from kHz to hundreds of MHz.
4. Communication Modules
> WiFi, Bluetooth, Ethernet, LoRa, or cellular modules for networked embedded systems (IoT devices).
5. Real-Time Operating System (RTOS) (optional)
> For complex systems requiring task scheduling and resource management, like FreeRTOS or Zephyr.

# 2. How They Work Together: The Lifecycle of a Data Point

Imagine a "Smart Thermostat" project. Here is the collaboration:

1. **Input:** A **Temperature Sensor** sends a tiny voltage to the **ADC**.
2. **Conversion:** The ADC converts that voltage into a digital value (e.g., `0x3FF`).
3. **Processing:** The **MCU** runs code to compare that value against a setpoint stored in its **Flash Memory**.
4. **Communication:** The MCU might send the current status to a screen via **SPI**.
5. **Output:** If it's too cold, the MCU sends a **GPIO** signal to a **Relay (Actuator)** to kick on the heater.


## 3. The "Hidden 20%": Knowledge for Professional Work

To move from "it works on my desk" to "it works in the field," you need this specific 20% of advanced knowledge:

### Hardware-Software Interfacing

* **Interrupts vs. Polling:** Don't let your code sit in a loop waiting for a button press (Polling). Use **Interrupts** so the CPU can "sleep" and only wake up when an event occurs.
* **Reading Datasheets:** This is the most underrated skill. You must be able to find the "Electrical Characteristics" table to ensure you don't fry your chip with the wrong voltage.

### The "Real-Time" Mindset

* **Deterministic Timing:** In embedded systems, "late" data is "wrong" data. If an airbag deployment signal is 100ms late, the system has failed.
* **RTOS (Real-Time Operating System):** For complex projects, you'll use an RTOS (like FreeRTOS) to manage multiple tasks simultaneously without them crashing into each other.

### Debugging & Reliability

* **The Logic Analyzer:** This tool lets you "see" the 1s and 0s moving across wires. It is 10x more useful than a multimeter for communication issues.
* **Static Memory:** Professionals avoid `malloc()` or dynamic memory. We define memory sizes at "compile-time" to prevent the system from crashing due to "memory leaks" months later.
