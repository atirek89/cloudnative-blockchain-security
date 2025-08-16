# Week 1- Networking Fundamentals

---

## Learning Objctives

By the end of this week, we will be able to:

1. Explain the OSI and TCP/IP models and their layers.
2. Configure IP addressing and perform subnetting calculations.
3. Understanding VLAN concepts and configure basic VLANs.
4. Capture and analyze packets using Wireshark/tcpdump.

---

## 1. OSI & TCP/IP Models

- **OSI Model Layers**\
  Open System Interconnection. Theoretical model

  Defines the communication standard between machines in a network.

  Network communication is divided into 7 Layers:
  1. **Physical** - represents the physical connection on the network for sending and receiving bits. Eg. cabels, connectors, signals
  2. **Data Link** - defines data transmission between 2 or more machines. Ethernet, PPP, MAC (Media Access Control) addresses
  3. **Network** - ensures packet routing between network nodes. IP addressing, routing
  4. **Transport** - choosed the best way to send informantion. Protcol - TCP/UDP
  5. **Session** - establishes, maintains, and terminates exchange sessions (communication management).
  6. **Presentation** - encodes, compresses, converts and reformats the data.
  7. **Application** - This layer is the access point to application services for the user. Protocols - Web (HTTP, HTTPS), Email(SMTP), File transfer (FTP, sFTP), DNS

- **TCP/IP Model Layers**\
  True implementation.

  The TCP/IP model simplifies the OSI model into 4 layers:

  1. Network Access - Combination of "Physical and Data Link". It handles the connection between 2 machines.
  2. Internet - Network layer, IP addressing
  3. Transport - TCP, UDP
  4. Application - Combination of "Application, Presentation, and Session" layers. It encompasses application protocols like HTTP, FTP, SMTP, SSH, DNS, etc.

  ```mermaid
        flowchart TB
            subgraph OSI_Model
                O1(Application)
                O2(Presentation)
                O3(Session)
                O4(Transport)
                O5(Network)
                O6(Data_Link)
                O7(Physical)
            end

            subgraph TCP_IP_Model
                T1(Application)
                T2(Transport)
                T3(Internet)
                T4(Network_Access)
            end

            O1 --> T1
            O2 --> T1
            O3 --> T1
            O4 --> T2
            O5 --> T3
            O6 --> T4
            O7 --> T4
  ```

- **PDU (Protocol Data Unit)**\
  This of a PDU as the "package of data" at each layer of the network model.
  Each layer of the TCP/IP stack **adds its own wrapper** to the data, just like packing a gift inside multiple boxes before shipping.

  - Each layer in TCP/IP has its own PDU name.
  - When sending data, each layer encapsulates it into its own PDU.
  - When receiving, the reverse happens — each layer removes its own wrapper (decapsulation).

  **PDU in Simple Terms**

  - **Application Layer PDU**: The raw message (email text, HTTP request, etc.) -> called **Data**
  - **Transport Layer PDU**: Adds delivery instructions like sender and receiver "apartment numbers" (ports) -> called a **Segment**.
  - **Internet Layer PDU**: Adds postal address (IP addresses) -> called a **Packet**.
  - **Network Access Layer PDU**: Adds street address and house number (MAC addresses) -> called a **Frame**.
  - **Physical Layer PDU**: Converts it to electrical signals / bits for travel -> called **Bits**

  | TCP/IP Layer   | Example Protocols | PDU Name                               | What’s Added                   |
  | -------------- | ----------------- | -------------------------------------- | ------------------------------ |
  | Application    | HTTP, FTP, SMTP   | **Data**                               | Nothing, it's your message     |
  | Transport      | TCP, UDP          | **Segment** (TCP) / **Datagram** (UDP) | Port numbers, sequence numbers |
  | Internet       | IP, ICMP          | **Packet**                             | IP addresses                   |
  | Network Access | Ethernet, Wi-Fi   | **Frame**                              | MAC addresses, CRC             |
  | Physical       | -                 | **Bits**                               | Signals                        |

  **Visual analogy (gift wrapping):**

  ```css
  [ Application Data ]  →  Data
  [ + Transport Header ]  →  Segment
  [ + IP Header ]         →  Packet
  [ + MAC Header & Trailer ] →  Frame
  [ Converted to Signals/Bits ]
  ```

  **Gift Analogy**
  - Inside: Gift (Application data)
  - Box 1: Transport label (Segment)
  - Box 2: Address label (Packet)
  - Box 3: Courier label (Frame)
  - Finally: Delivered as physical movement (Bits).

    ```mermaid
    flowchart TB
        subgraph TCP_IP_Model
            T1(Application)
            T2(Transport)
            T3(Internet)
            T4(Network_Access)
        end

        subgraph PDU
            P1(Data)
            P2(Segments)
            P3(Packets)
            P4(Frames)
            P5(Bits)
        end

        T1 --> P1
        T2 --> P2
        T3 --> P3
        T4 --> P4
        P4 --> P5
    ```

- **Resources**:
  1. [OSI & TCP/IP Models Explained in 9 minutes](https://www.youtube.com/watch?v=6fYsCkvuPbw&t=8s)
  2. [OSI and TCP IP Models - Best Explanation](https://www.youtube.com/watch?v=3b_TAYtzuho)
  3. [Demystifying Protocol Data Units (PDUs) and Understanding Network Building Blocks](https://www.youtube.com/watch?v=wqxDQuFnNjI)

## 2. IP Addressing & Subnetting

- ### IP Addressing

  - **What is an IP Address?**
    Think of an **IP address** like a **house address in a digital city**.
    - The **street** = Network part (tells which neighborhood the house is in)
    - The**house number** = Host part (which exact building in that neighborhood)

    Example: `192.168.1.10`
    - `192.168.1` -> Network
    - `10` -> Host

    All devices in the same network share same **network part** but have **different host parts**.

  - **Type of IP Addresses**
    We use **IPv4** (mostly) and **IPv6** (future).

    **IPv4 (32 bits = 4 octets)**

    Example: `192.168.1.10`
    - Stored in binary: `11000000.10101000.00000001.00001010`
    - Each octet (0-255) is separated by dots.

  - **Public vs Private IP**
    - **Public** -> Reachable on the internet
    - **Private** -> Used inside local network

    The IETF (RFC 1918) reserved certain address blocks for private use only. These addresses:
    - Are **not routable** on the public internet.
    - Are meant for **internal networks** (home, office, lab)
    - Need a **NAT gateway** to talk to the outside world.

    **The Three Private IP ranges:**

    | Range                             | Size                               | Example Use                               |
    | --------------------------------- | ---------------------------------- | ----------------------------------------- |
    | **10.0.0.0 → 10.255.255.255**     | **16,777,216** addresses (Class A) | Huge corporations, ISPs internal networks |
    | **172.16.0.0 → 172.31.255.255**   | **1,048,576** addresses (Class B)  | Medium to large organizations             |
    | **192.168.0.0 → 192.168.255.255** | **65,536** addresses (Class C)     | Homes, small offices                      |

    **Visual: Think of it like Three Apartment Buildings**

    - Building A (10.x.x.x) -> Has 16 million rooms.
    - Building B (172.16.x.x - 172.31.x.x) -> Has 1 million rooms.
    - Building C (192.168.x.x) -> Has 65 thousand rooms.

    They all exists inside the colony (private network) and connect to the outside world through the main gate (NAT).

    **Reality:**
    - Home router probably gives 192.168.x.x -> because it's small & easy to manage.
    - Corporate networks often use 10.x.x.x for flexibility.
    - You'll rarely see 172.16-172.31 unless you work with certain enterprise networks or cloud setups (AWS VPC often uses 172 ranges).

  - **NAT (Network Address Translation)**
    Imagine home Wi-Fi:
    - All devices (phone, laptop, TV) have a private IPs like `192.168.1.10`, `192.168.1.10`, etc.
    - The internet has no idea about these IPs - it only sees home **router's public IP**.

    **How NAT Works**
    - NAT sits on our router or firewall.
    - When our devices sends a packet to the internet:
      1. NAT **replaces our private IP** with our router's **public IP**.
      2. It **remembers the mapping** (e.g., Laptop ->  Port 40001, Phone -> Port 40002).
      3. When the reply comes back, NAT **swaps it back** to the right device.

    **Types of NAT**
    1. **Static NAT** - One private IP maps to one public IP (like a personal translator)
    2. **Dynamic NAT** - A pool of public IPs shared among private IPs
    3. **PAT (Port Address Translation)** - Many private IPs share a **single public IP** using different port numbers (**this is what our home Wi-Fi does**).

    **NAT Gateway in the Cloud**

    In AWS, GCP, Azure, etc., a **NAT Gateway**:
    - Lets **private subnet resources** (like EC2 instances without public IPs) access the internet **outbound only**.
    - No inbound internet traffic can reach them unless explicitly configured.
    - Acts like our **router's NAT**, but managed by the cloud provider.

    **Example:**
    - Private EC2 (`10.0.2.14`) needs to download security patches.
    - It sends request - NET Gateway replaces `10.0.2.14` with public IP `3.15.23.88` -> Internet replies -> NAT Gateway sends back to `10.0.2.14`.

      ```css
      [Private IP device] → (Private IP)
            |
            v
      [Router/NAT Gateway] → (Public IP)
            |
            v
      [Internet]
      ```

- ### Subnetting

  - **Subnet Mask - The Divider**
    A Subnet Mask tells us where the network ends and hosts begin.

    Example:

    ```nginx
      IP Address: 192.168.1.10
      Subnet Mask: 255.255.255.0
    ```

    - `255` -> network portion (fixed, can't change)
    - `0` -> host portion (can vary)

    So here:
    - Network = `192.168.1`
    - Hosts = `.1` to `.254` (`.0` = network ID, `.255` = broadcast)

    **Note:-** `255.255.255.0` means the first 3 parts (24 bits) are for the network, last 8 bits for hosts.

    **Binary View of Subnet Mask**

    A Subnet Mask is just 1s and 0s:
    - `1` = "Network"
    - `0` = "Host"

    Example: `255.255.255.0`

    ```text
    11111111.11111111.11111111.00000000
    ```

    - First 24 bits = **Network ID**
    - Last 8 bits = **Host ID**

  - **CIDR Notation (Classless Inter-Domain Routing)**

  Instead of writing the mask fully,w e write /N where N = number of network bits.

  `255.255.255.0` = 24 ones -> /24

  **Example:**

  | Subnet Mask     | Binary Representation               | CIDR |
  | --------------- | ----------------------------------- | ---- |
  | 255.0.0.0       | 11111111.00000000.00000000.00000000 | /8   |
  | 255.255.0.0     | 11111111.11111111.00000000.00000000 | /16  |
  | 255.255.255.0   | 11111111.11111111.11111111.00000000 | /24  |
  | 255.255.255.192 | 11111111.11111111.11111111.11000000 | /26  |
  | 255.255.255.224 | 11111111.11111111.11111111.11100000 | /27  |

  **Binary Place Values**

  Each bit in 8 bits has a value (powers of 2):  `128 64 32 16 8 4 2 1`

  So, the binary number `11111111` = 128+64+32+16+8+4+2+1 = 255.
  That's why the subnet mask uses 255s!

  **Calculation for /27:-**

  - Mask: 255.255.255.224
  - Binary(last octet): 11100000
  - CIDR: /27 (because these are 27 ones "24+3")
  - Host bits: 32-27 = 5
  - Usable hosts: 2^5-2 = 30
  - Block size: 256-224 = 32 (subnets jump by 32 in the last octet)

  **Host bits, Usable hosts, Block size - What they mean?**

  - **Host bits**
    - IPv4 = 32 bits total.
    - **Host bits = 32 - (CIDR prefix)**.

      Example: `/26` -> host bits = 32-26 = 6

    Host bits are the `zeros` in the subnet mask (the part you can vary inside the subnet).

  - **Usable Host**
    - If hosts bits >= 2, **Usable = 2^(host bits) - 2**

      (minus 2 for **network ID** and **broadcast**).

    - Special cases:
      - `/31` -> 2 address, typically **0 usable** in classic LANs (used for point-to-point links).
      - `/32` -> 1 address, **0 usable** (identifies a single host).

    **Examples:**
    - `/24` -> host bits 8 -> usable = 2^8 - 2 = 254
    - `/27` -> host bits 5 -> usable = 2^5 - 2 = 30
    - `/30` -> host bits 2 -> usable = 2^2 - 2 = 2

  - **Block size (a.k.a "increment")**

    This tells us **how far each subnet jumps** in the "interesting octet" (the octet where the mask stops being 255 and starts having non-255 value).

    **Formula:** `Block size = 256 - (mask value in the interesting octet)`

    **Examples:**
    - `/27` mask = `255.255.255.224` -> interesting octet = 4th (224)

      Block size = 256-224 = 32 -> networks at 0,32,64,96,128,160,192,224.

    - `/20` mask = `255.255.240.0` -> interesting octet = 3rd (240)

      Block size = 256-240 = 16 -> networks at 0,16,32,....,240 in the third octet.

    How to find the interesting octet:
    - Divide the prefix by 8.
      - `/27` = 24 full bits (3 full octets) + 3 bits into octet 4 -> interesting octet = 4th.
      - `20` = 16 full bits (2 full octets) + 4 bits into octet 3 -> interesting octet = 3rd.

  - **Step by step recipe to Subnet Calculations**

    Given **IP** and **/prefix**:
    1. **Host bits** = 32 - prefix.
    2. **Subnet mask** -> (optional) convert prefix to dotted mask.
    3. **Find interesting octet** (where mask != 255 and != 0).
    4. **Block size** = 256 - (mask value in interesting octet).
    5. **Find the network ID**:
        - Take the value of the IP in the interesting octet.
        - **Floor** it to nearest multiple of the block size. That's the **network's octet value**.
        - All lower octets become **0**.
    6. **Broadcast**: Same as network but add (block size -1) in the interesting octet, and set all lower octets to 255.
    7. **Usable range**: network + 1 -> broadcast - 1.
    8. **Usable hosts count**: 2^(host bits) - 2/.

  - **Worked Examples**
    - **Example A - `/27`**
      - Host bits: `32 - 27 = 5`
      - Mask: `255.255.255.224`
      - Interesting octet: `224 -> 4th octet`
      - Block size = 256 - 224 = 32
      - Network IDs: There would be total 8 block of each of size 32, and there would be 1 network IDs each block so total 8 network ID's like: `192.168.1.0, 192.168.1.32, 192.168.1.64, 192.168.1.96, 192.168.1.128, 192.168.1.160, 192.168.1.192, 192.168.1.224`
      - Broadcast: Same as network there would be 8 broadcast (block size - 1): `192.168.1.31, 192.168.1.63, 192.168.1.95, 192.168.1.127, 192.168.1.159, 192.168.1.191, 192.168.1.223, 192.168.1.255`

        Example: IP `192.168.1.141` belongs to **Subnet 5** -> Network = `192.168.1.128/27`, Broadcast = `192.168.1.159`.

      - Usable range - There would be 30 usable IPs in each block
        - Subnet/Block 1 - `192.168.1.1   - 192.168.1.30`
        - Subnet/Block 2 - `192.168.1.33  - 192.168.1.62`
        - Subnet/Block 3 - `192.168.1.65  - 192.168.1.94`
        - Subnet/Block 4 - `192.168.1.96  - 192.168.1.126`
        - Subnet/Block 5 - `192.168.1.129 - 192.168.1.158`
        - Subnet/Block 6 - `192.168.1.161 - 192.168.1.190`
        - Subnet/Block 7 - `192.168.1.193 - 192.168.1.222`
        - Subnet/Block 8 - `192.168.1.225 - 192.168.1.254`

      - Usable hosts - 2^5-2 = 32 - 2 = 30 hosts per subnet

    - **Example B - `/20`**
      - Host bits: `32 - 20 = 12`
      - Mask: `255.255.240.0`
        Binary: `11111111.11111111.11110000.00000000`
      - Interesting octet: `240 -> 3rd octet`
      - Block size = 256 - 240 = 16
      - Network IDs: There would be total 16 block of each of size 16, and there would be 1 network IDs each block so total 16 network ID's like: `172.16.0.0, 16, 32, 48, 64, 80, 96, 112, 128, 144, 160, 176, 192, 208, 224, 240`
      - Broadcast: Same as network there would be 16 broadcast (block size - 1): `172.16.15.255, 31, 47, 63, 79, 95, 111, 127, 143, 159, 175, 191, 207, 223, 239, 255`

        Example: IP `172.16.141.77/20` belongs to **Subnet 9** -> Network = `172.16.128.0`, Broadcast = `172.16.143.255`.

      - Usable hosts - 2^12-2 = 4096 - 2 = 4094 hosts per subnet

      - Usable range - There would be 4094 usable IPs in each block
        - Subnet/Block 1 `172.16.0.0/20 subnet` - `172.16.0.1   - 172.16.15.254`
        - Subnet/Block 2 `172.16.16.0/20 subnet` - `172.16.16.1  - 172.16.31.254`
        - Subnet/Block 3 `172.16.32.0/20 subnet` - `172.16.32.1  - 172.16.47.254`
        - ...
        - ...
        - ...
        - Subnet/Block 16 `172.16.140.0/20 subnet` - `172.16.240.1  - 172.16.255.254`

  - **Subnetting - Breaking Network**

    Imagine you have a big colony and you want to divide it into smaller gated communities.

    Example:
    `192.168.0/24 -> 1 Network, 254 Hosts`

    If you want **4 smaller networks:**
    - New mask: `/26` (255.255.255.192)
    - Each subnet = 64 IPs (62 usable hosts)
    - Networks:
      - 192.168.1.0/26 (hosts .1 -> .62)
      - 192.168.1.64/26 (hosts .65 -> .126)
      - 192.168.1.128/26 (hosts .129 -> .190)
      - 192.168.1.192/26 (hosts .193 -> .254)

  - **Memory Hooks**

    Here's how to **never forget**:
    1. **"255" Means Lock**
        - 255 = network (can't change)
        - 0 = free (can assign to host)
    2. **2's Power Rule**
        - Hosts per subnet = 2^H-2 (H = host bits)
        - Subnets created = 2^S (S = borrowed bits from host part)
    3. **Binary Blocks**

        Subnet mask octet values come from binary:

        `128 192 224 240 248 252 254 255`
        (These are the only valid subnet mask values, in order.)

  - **Relation to TCP/IP Model**
    - **IP addressing & Subnetting** live in **Internet Layer** of TCP/IP (Network layer in OSI)
    - They help routing packets between networks.
