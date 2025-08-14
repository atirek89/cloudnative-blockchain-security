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

    
