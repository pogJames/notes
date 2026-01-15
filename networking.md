## **1. The OSI Model (The 7-Layer Cake)**

The **OSI (Open Systems Interconnection)** model is a conceptual framework used to understand how data moves across a network. While the internet actually runs on a simpler model (TCP/IP), the OSI model is the industry standard for troubleshooting.

* **Layer 1: Physical** – Cables, connectors, and electricity (bits).
* **Layer 2: Data Link** – Moves data between two physically connected nodes. This is where **MAC Addresses** live.
* **Layer 3: Network** – Routes data between different networks. This is where **IP Addresses** live.
* **Layer 4: Transport** – Ensures data gets there reliably. This is where **TCP** and **UDP** live.
* **Layers 5–7: Session, Presentation, Application** – These handle the data logic, encryption, and the final interface (like **HTTP** or **FTP**).


## **2. Router vs. Switch**

In the 80/20 of networking hardware, you just need to know which layer they operate on.

* **Switch (Layer 2):** Connects devices *within* the same local network (LAN). It uses **MAC addresses** to send data to the correct port. Think of it as a power strip for data inside one room.
* **Router (Layer 3):** Connects *different* networks together (e.g., connecting your home LAN to the Internet). It uses **IP addresses** to decide which "neighborhood" the data needs to go to.

> **The Rule of Thumb:** Switches create networks; Routers connect networks.


## **3. Configuring IPv4 vs. IPv6**

Every device needs an address to communicate.

* **IPv4 (The Old Standard):** Uses 32-bit addresses written in decimal (e.g., `192.168.1.1`). Because there are only ~4.3 billion addresses, we ran out years ago.
* **Config:** Requires a **Subnet Mask** (to define network size) and a **Default Gateway** (the router's IP).


* **IPv6 (The New Standard):** Uses 128-bit addresses written in hexadecimal (e.g., `2001:0db8:85a3...`). There are enough addresses for every grain of sand on Earth.
* **Config:** Often uses **SLAAC** (Stateless Address Autoconfiguration), meaning devices can often generate their own IP without a central server.



## **4. Device Discovery**

How does a computer find another computer on a network it just joined?

* **ARP (Address Resolution Protocol):** The most common way. Your computer yells: *"Who has IP 192.168.1.5? Tell me your MAC address!"* * **mDNS (Multicast DNS):** Used by printers and Apple devices (Bonjour). It allows devices to find each other using names like `printer.local` without a central server.
* **LLDP / CDP:** Protocols used by switches and routers to "introduce" themselves to their neighbors.


## **5. Tools for Topology Discovery**

If you walk into a server room and don't know how things are connected, you use these:

* **Ping / Traceroute:** Tells you if a device is alive and exactly which routers the data passes through to get there.
* **Nmap:** The "Swiss Army Knife." It scans a network to find every active IP, what ports are open, and what OS they are running.
* **SNMP (Simple Network Management Protocol):** A protocol that lets management software "ask" switches and routers for their internal maps.
* **Visualizers:** Tools like **Wireshark** (to see raw packets) or **NetBrain** (to draw the map automatically).
