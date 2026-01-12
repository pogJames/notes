## 1. The Core Components of a SoM

A SoM integrates the high-density "computing core" onto a small, multi-layer PCB. Its primary components include:

* **The SoC (System on Chip):** This is the main processor (e.g., an ARM Cortex-A or i.MX series). It contains the CPU, GPU, and many internal controllers (USB, Ethernet MAC).
* **Volatile Memory (RAM):** Usually DDR3, DDR4, or LPDDR4. These chips are soldered very close to the SoC because the electrical signals move so fast they require "length-matched" traces that are difficult to design from scratch.
* **Non-Volatile Storage (Flash):** Usually **eMMC** or **NAND Flash**. This holds your Operating System (Linux/Android), drivers, and application code.
* **PMIC (Power Management IC):** A specialized chip that takes a single input voltage (e.g., 5V) and splits it into the 10+ different precise voltages the CPU and RAM need to boot up in a specific order.
* **Ethernet PHY:** While the "brain" handles the data, the PHY (Physical Layer) chip handles the actual electrical signals for wired networking.
* **Oscillators/Crystals:** These provide the "heartbeat" (clock signal) for the processor.

## 2. How They Work Together: The SoM vs. Carrier Board

A SoM cannot work alone. It follows a "Brain and Body" architecture:

1. **The SoM (The Brain):** Handles the high-speed processing, operating system, and memory management. It usually has no physical ports (no USB jacks or HDMI sockets).
2. **The Carrier/Development Board (The Body):** This is the custom board *you* design. It contains the physical connectors (USB ports, RJ45, Screen headers) and power input.
3. **The Interface:** The SoM connects to your carrier board via **high-density board-to-board connectors** or **edge connectors** (like a RAM stick in a PC).

> **Why this matters:** If you need to upgrade your system's power in two years, you don't need to redesign your whole device. You simply swap the old SoM for a faster one that fits the same connector.


## 3. Extra SoM Knowledge

Since the SoM handles the hardest hardware, your "Vital 20%" of knowledge shifts toward **integration and software**:

* **BSPs (Board Support Packages):** When you buy a SoM, the vendor provides a BSP. This is a collection of drivers and a pre-configured version of Linux. You must learn how to "build" this (often using tools like **Yocto** or **Buildroot**).
* **Device Trees:** In embedded Linux, the software doesn't "know" what is plugged into your carrier board. You have to write a **Device Tree file**—a simple text file that tells the Linux kernel: *"There is an LED on Pin 4 and a Screen on the I2C bus."*
* **Thermal Management:** SoMs pack a lot of power into a tiny space. You need to know how to design a **heatsink or thermal pad** to pull heat away from the SoM into your carrier board or case.
* **Pin Multiplexing (PinMux):** Modern SoCs have pins that can do multiple things (e.g., a pin could be a GPIO *or* part of a UART). You’ll need to use a "PinMux tool" provided by the manufacturer to configure these pins for your specific carrier board layout.
