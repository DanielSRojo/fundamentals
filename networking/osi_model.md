# OSI Model

The **OSI model** (Open Systems Interconnection) is a conceptual framework that divides network communication in to seven layers, from physical transmission of bits to application-level protocols. It helps understand, design, and troubleshoot networked systems by separating concerns into clear, ordered stages.

While modern networking primarily uses **TCP/IP model**, the OSI model remains one of the best tools for reasoning about how data moves through a system.

## The Seven Layers

| **Layer** | **Name**     | **Concept**                         | **Examples**                    |
| --------- | ------------ | ----------------------------------- | ------------------------------- |
| **7**     | Application  | User-facing protocols & interfaces  | HTTP, HTTPS, SSH, DNS, SMTP     |
| **6**     | Presentation | Data formats, encoding, compression | TLS, JSON, XML, ASCII, JPEG     |
| **5**     | Session      | Sessions, connections, state        | TLS handshake, RPC session mgmt |
| **4**     | Transport    | End-to-end delivery, reliability    | TCP, UDP, QUIC                  |
| **3**     | Network      | Routing packets across networks     | IP, ICMP, OSPF, BGP             |
| **2**     | Data Link    | Frame delivery within a network     | Ethernet, Wi-Fi, ARP, VLANs     |
| **1**     | Physical     | Transmission of bits over a medium  | Cables, radio, fiber, voltages  |

### 1. Physical Layer

Defines the **electrical/optical** signals used to transmit bits. Cables, NICs, voltages, frequencies, modulation.

### 2. Data Link Layer

Provides **frames**, MAC addresses, and local network delivery. Defines how devices share a medium (switches, Wi-Fi APs).

### 3. Network Layer

Handles **routing** and logical addressing (IP). Forwards packets between networks.

### 4. Transport Layer

Ensures end-to-end data delivery between hosts. Reliability (TCP), speed and simplicity (UDP), multiplexing.

### 5. Session Layer

Manages **sessions** between endpoints: open, close, resume. Most of its responsibilities today are absorbed by transport or application protocols.

### 6. Presentation Layer

Defines **data representation**: serialization, encryption, compression.

### 7. Application Layer

Where protocols interact with the user or application logic. HTTP, DNS, SSH, etc.

## Why It Matters

Understanding the OSI model is extremely useful for:

- troubleshooting network issues
- understanding where encryption happens
- separating concerns between layers
- mapping modern protocols into a conceptual model

Example:

- **Packet loss**: likely layer 1, 2 or 3
- **TLS handshake failing**: layer 5/6
- **Web API error**: layer 7

## Mapping the OSI Model to the Real Internet (TCP/IP)

Real world networking uses the **TCP/IP model**, which roughly maps like:

| **OSI Layer** | **TCP/IP**  |
| ------- | ----------- |
| 7, 6, 5 | Application |
| 4       | Transport   |
| 3       | Internet    |
| 2, 1    | Link        |
