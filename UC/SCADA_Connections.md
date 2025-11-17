Of course. Based on the provided documents, here is a comprehensive breakdown of the services, ports, and connections needed for everything to connect to a SCADA system, categorized by the type of connection.

### Executive Summary

Connecting to a SCADA system involves two primary layers:
1.  **The Physical/Protocol Connection:** How the data is formatted and physically transmitted (e.g., Modbus TCP, DNP3, Serial).
2.  **The Network Transport:** The underlying network path the data takes (e.g., direct LAN, corporate WAN, the internet).

Each combination of these layers requires specific **ports** to be open on firewalls and specific **network configurations** to allow traffic to flow.

---

### Category 1: Simple, Direct Connections (Local/Plant Floor)

These are used for devices within a control panel or on a local plant network.

#### 1. Modbus TCP
*   **Purpose:** The universal "language" for connecting basic industrial devices (sensors, meters, PLCs, drives) to a SCADA system.
*   **Protocol & Port:**
    *   **Protocol:** Modbus TCP (runs over standard Ethernet TCP/IP).
    *   **Default Port:** **502**. This is the universally recognized port for Modbus TCP.
*   **Connections Needed:**
    *   The SCADA server (Master) must be able to initiate a TCP connection to the IP address and port 502 of the field device (Slave).
    *   This is typically done on a local, isolated **LAN** (e.g., a "Plant Control Network").
*   **Security Note:** As your document states, this is not secure for untrusted networks. The Modbus protocol itself has no security. It is crucial to isolate this network from the corporate IT network and the internet using a firewall.

#### 2. WT32-ETH01 Board (with Modbus TCP)
*   **Purpose:** A custom hardware solution to create a simple, low-cost Modbus TCP server for basic I/O (reading sensors, controlling relays).
*   **Protocol & Port:**
    *   **Protocol:** Modbus TCP.
    *   **Default Port:** **502** (configurable in the device's code).
*   **Connections Needed:**
    *   Identical to a standard Modbus TCP device. The SCADA system connects to the board's IP address on port 502.
    *   It resides on the same local control LAN as other Modbus devices.

#### 3. SCADA-Xchange Interface (Protocol Gateway)
*   **Purpose:** To translate between proprietary or less common protocols (e.g., from Xylem pumps) into a standard SCADA protocol like Modbus TCP or DNP3.
*   **Protocol & Port:**
    *   **Side A (Field Devices):** Connects to proprietary devices. This could be via **Serial (e.g., RS-485)** or a proprietary network. The ports here are physical serial ports, not network ports.
    *   **Side B (SCADA System):** Presents a standard protocol to the SCADA system.
        *   If using **Modbus TCP**, it will use port **502**.
        *   If using **DNP3**, it will use port **20000** (see below).
*   **Connections Needed:**
    *   The SCADA system connects to the SCADA-Xchange's IP address on the standard port of the chosen output protocol (502 or 20000).

---

### Category 2: Larger Network or Remote Site Connections

These are designed for secure and reliable communication over longer distances, often between a central SCADA server and remote field sites.

#### 1. DNP3 (Distributed Network Protocol)
*   **Purpose:** The industry standard for utility SCADA (water, electric, oil & gas). It is robust, secure, and designed for reliable data reporting from Remote Terminal Units (RTUs).
*   **Protocol & Port:**
    *   **Protocol:** DNP3 over TCP/IP or UDP/IP. TCP is more common for reliable session-based communication.
    *   **Default Port:** **20000**. This is the IANA-assigned port for DNP3.
*   **Connections Needed:**
    *   The SCADA Master (at the central office) and the DNP3 Outstation (RTU, like one served by a SCADA-Xchange) must be able to communicate over an IP network.
    *   This requires a **persistent network connection**, which could be a private WAN, a VPN over the internet, or a cellular data network.
    *   Firewalls between the central site and the remote site must allow traffic on **port 20000** between the specific IP addresses of the Master and Outstations.

#### 2. Serial Links
*   **Purpose:** A classic, simple, and highly reliable point-to-point connection. Often used to connect a local SCADA workstation to a nearby PLC or RTU.
*   **Protocol & Port:**
    *   **Protocol:** Not a network protocol. Common serial protocols include **Modbus RTU/ASCII**, or proprietary ones.
    *   **Port:** This is a **physical port** (e.g., RS-232, RS-485) on the computer and device. It does not use an IP address or TCP/UDP port.
*   **Connections Needed:**
    *   A physical serial cable (often with specific pinouts) directly between the two devices.
    *   For longer distances, serial data can be converted to Ethernet using **Serial Device Servers**. In this case, the device server gets an IP address, and the SCADA system connects to a specific TCP port on the device server (e.g., port 4001 is common for raw socket connections), which then tunnels the data to the serial device.

#### 3. TCP/IP and LAN/WAN Networks (The Backbone)
*   **Purpose:** This is not a specific SCADA protocol but the transport for all the protocols above. It enables the "wide area" part of SCADA.
*   **Protocol & Port:**
    *   **Protocol:** Standard Internet Protocol (IP) suite.
    *   **Ports:** Varies based on the SCADA protocol being carried (502, 20000, etc.).
*   **Connections & Services Needed:**
    *   **LAN (Local Area Network):** Standard Ethernet switches to connect devices within a building or site.
    *   **WAN (Wide Area Network):** Leased lines (e.g., MPLS), radio links, or cellular networks (4G/5G) to connect remote sites to the central control room.
    *   **VPN (Virtual Private Network):** **This is critical for security over the internet.** A VPN creates an encrypted "tunnel" over a public network. Instead of opening port 20000 to the public internet, you would open the standard VPN port (e.g., **port 500 for IPsec**, **port 443 for SSL VPN**) on your firewall. All SCADA traffic (DNP3, Modbus) is then securely routed through this encrypted tunnel.
    *   **Firewalls:** Essential for securing the SCADA network. They must be configured with specific rules (Access Control Lists or ACLs) that only allow necessary traffic from specific source IPs to specific destination IPs on the specific required ports (502, 20000).

---

### Summary Table of Common SCADA Ports

| Protocol/Service | Default TCP Port | Default UDP Port | Purpose |
| :--- | :--- | :--- | :--- |
| **Modbus TCP** | **502** | - | Universal protocol for connecting PLCs, sensors, and meters. |
| **DNP3** | **20000** | 20000 | Robust protocol for utility and critical infrastructure RTUs. |
| **OPC UA** | 4840 | - | Modern, secure, and platform-independent data exchange. |
| **IEC 60870-5-104** | 2404 | - | A European standard for telecontrol, similar to DNP3. |
| **VPN (IPsec)** | - | **500** (IKE), **4500** (NAT-T) | Creates a secure, encrypted tunnel over untrusted networks. |
| **VPN (SSL)** | **443** (HTTPS) | - | Often used for client-to-site VPNs, easily bypasses firewalls. |
| **Serial Device Server** | Configurable (e.g., 4001) | - | Converts a physical serial port to a network socket. |

### Critical Security Principle: Defense in Depth

As your documents correctly warn, network security is paramount. **Never expose SCADA ports directly to the internet.** The recommended architecture is:

1.  **Network Segmentation:** Isolate the SCADA control network from the corporate IT network using a firewall.
2.  **Demilitarized Zone (DMZ):** Use a DMZ for any data historians or interfaces that need to talk to both the SCADA network and the corporate network. The SCADA system never has a direct connection to the corporate network.
3.  **VPN for Remote Access:** All remote access for maintenance or monitoring must go through a secure VPN.
4.  **Restrictive Firewall Rules:** Firewalls should be configured with a "default deny" policy, only explicitly allowing the necessary traffic on the required ports between specific devices.
