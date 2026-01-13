## 1. Low-Speed "Control" Interfaces

| Interface | Software Role | Key Config Parameters | Common "Software" Bug |
| --- | --- | --- | --- |
| **UART** | Serial terminal, GPS, Modems. | Baud Rate, Parity, Stop Bits. | **Buffer Overrun:** CPU doesn't read the FIFO fast enough, losing bytes. |
| **I2C** | Low-speed sensors (Temp, Accel). | 7-bit Device Address, Clock Speed (100k/400k). | **Blocking Bus:** A non-responsive slave hangs the `while(waiting_for_ack)` loop. |
| **SPI** | High-speed sensors, SD Cards, LCDs. | Clock Polarity (CPOL), Phase (CPHA), Bit Order. | **CS Pin Management:** Manual "Chip Select" toggling timing is too fast for the peripheral. |

---

## 2. High-Speed "Data" Interfaces

**DMA** and **Memory Buffers**.

| Interface | Software Role | Software "Vital 20%" | The "What" |
| --- | --- | --- | --- |
| **USB 2.0** | Generic peripherals (HID, Storage). | **Enumeration:** Host asks device for its "Descriptor" to load the right driver. | Uses EHCI (Enhanced Host Controller Interface) drivers. |
| **USB 3.0** | High-speed cameras/disks. | **Bulk Streams:** Managing multiple data streams over a single pipe. | Uses XHCI drivers; significantly higher CPU overhead for software. |
| **PCIe** | High-end Wi-Fi, NVMe, AI Accel. | **Enumeration & BARs:** OS maps the device's registers into the CPU's memory space. | Memory-mapped I/O; feels like writing to RAM once set up. |
| **RGMII** | Wired Networking (Gigabit). | **MDIO/MDC:** A side-channel "I2C-like" bus used to configure the Ethernet PHY chip. | You manage the "MAC" (Media Access Control) driver in the kernel. |

---

## 3. Multimedia Interfaces (MIPI)

**Linux Device Tree** and **V4L2 (Video for Linux)** framework.

| Interface | Software Role | Crucial Software Info |
| --- | --- | --- |
| **MIPI CSI** | Camera input. | **Lane Mapping:** You must tell the driver which physical lanes correspond to which logical IDs. |
| **MIPI DSI** | Display output. | **Vertical/Horizontal Timings:** Front porch, back porch, and sync pulse widths in pixels. |

---

## 4. The Software Developer’s "Gotcha" List

When debugging these interfaces, look for these three software-side failures first:

1. **Endianness:** SPI and I2C often send the Most Significant Bit (MSB) first. If your data looks like gibberish, you likely need to swap bytes in your software buffer.
2. **Interrupt vs. Polling:** If your UART data is corrupted at high speeds, you likely need to switch from **Polling** (checking in a loop) to **Interrupt-Driven** or **DMA** (Direct Memory Access) transfers.
3. **The Register Map:** Every I2C/SPI device has a datasheet "Register Map." You aren't just "sending data"; you are writing `Value X` to `Address Y`. Always verify the base address first.

---

### Comparison: Complexity vs. Throughput

| Interface | Throughput | Driver Difficulty |
| --- | --- | --- |
| **UART** | ~115 Kbps | Very Easy (Byte-level) |
| **I2C/SPI** | 1 - 50 Mbps | Easy (Register-level) |
| **USB 2.0** | 480 Mbps | Hard (Stack-level) |
| **MIPI CSI** | 1 - 10 Gbps | Very Hard (Kernel/Framework) |
| **PCIe** | 32 Gbps+ | Expert (OS/Bus Enumeration) |
