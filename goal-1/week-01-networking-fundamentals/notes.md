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
- **OSI Model Layers** - 
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

- **TCP/IP Model Layers** - 
    True implementation. 

    The TCP/IP model simplifies the OSI model into 4 layers:

    1. Network Access - Combination of "Physical and Data Link". It handles the connection between 2 machines.
    2. Internet - Network layer, IP addressing
    3. Transport - TCP, UDP
    4. Application - Combination of "Application, Presentation, and Session" layers. It encompasses application protocols like HTTP, FTP, SMTP, SSH, DNS, etc.

- **PDU (Protocol Data Unit)**


- **Resources**:
    1. [OSI & TCP/IP Models Explained in 9 minutes](https://www.youtube.com/watch?v=6fYsCkvuPbw&t=8s)
    2. [OSI and TCP IP Models - Best Explanation](https://www.youtube.com/watch?v=3b_TAYtzuho)