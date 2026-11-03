# Wireshark Deep Packet Forensics & SQA-Driven Root Cause Analysis (RCA) Runbook

[![Live Demo](https://img.shields.io/badge/Demo-Interactive%20Wireshark%20Packet%20Dissector-06b6d4)](https://mhmithun.github.io/05-Wireshark-Network-Forensics-RCA/)
[![License: MIT](https://img.shields.io/badge/License-MIT-emerald.svg)](LICENSE)
[![Analysis: Wireshark](https://img.shields.io/badge/Sniffer-Wireshark%20%2F%20tshark-blue.svg)](docs/wireshark_display_filters_cheat_sheet.md)
[![Methodology: SQA RCA](https://img.shields.io/badge/Standard-IEEE%20829%20%2F%20ITIL%20RCA-emerald.svg)](docs/RCA_01_DNS_Resolution_Latency.md)

Production troubleshooting framework bridging **Wireshark packet capture telemetry** with rigorous **Software Quality Assurance (SQA)** defect triage and **ITIL Root Cause Analysis (RCA)**. Solves ISP and enterprise latency, TCP retransmission storms, MTU blackholes, and rogue broadcast loops.

---

## 🌐 Live Web Packet Dissector (`index.html`)

An interactive browser-based Wireshark simulator:
- **Interactive Packet Table**: Browse real packet streams (Frame Number, Timestamp, Source/Destination IP, Protocol, Length, and Info).
- **Packet Dissector Tree View**: Expand headers across Layer 2 (Ethernet II), Layer 3 (IPv4), Layer 4 (TCP/UDP), and Layer 7 (DNS, HTTP, DHCP).
- **Raw Hex / ASCII Payload Inspector**: Click any packet byte offset to inspect payload hexadecimal and decoded ASCII.
- **Wireshark Display Filter Engine**: Filter packets on the fly with syntax like `tcp.analysis.retransmission`, `dns.flags.rcode != 0`, `icmp`, and `bootp`.
- **RCA Incident Generator**: Click "Export RCA Report" to format observed packet defects into structured bug reports for network engineers.

---

## 🛠️ Case Studies & Documented RCAs

1. **[Case 01: Intermittent DNS Timeout & SERVFAIL Inquiries](docs/RCA_01_DNS_Resolution_Latency.md)**
   - *Symptom*: End users experiencing 5-second delays opening internal web portals.
   - *Wireshark Analysis*: `dns.flags.response == 0` without response within 4.8 seconds; root server queried instead of local recursive forwarder.
   - *Remediation*: Tuned `resolv.conf` timeout and configured BIND9 forward-only zone.

2. **[Case 02: PPPoE MTU Blackhole & TCP SYN/ACK Stall](docs/RCA_02_TCP_Retransmission_Blackhole.md)**
   - *Symptom*: Broadband customers can ping websites but HTTPS web pages spin indefinitely.
   - *Wireshark Analysis*: Client sends 1500-byte frame; PPPoE 8-byte overhead causes `ICMP Type 3 Code 4 (Fragmentation Needed and DF set)` dropped by upstream firewalls.
   - *Remediation*: Configured MikroTik `/ip firewall mangle add chain=forward action=change-mss new-mss=clamp-to-pmtu`.

3. **[Wireshark Display Filter Cheat Sheet](docs/wireshark_display_filters_cheat_sheet.md)**:
   - High-impact filters for ISP NOC operations.

---

## 📁 Repository Structure
```
├── docs/
│   ├── RCA_01_DNS_Resolution_Latency.md
│   ├── RCA_02_TCP_Retransmission_Blackhole.md
│   └── wireshark_display_filters_cheat_sheet.md
├── index.html                           # Interactive packet dissector simulator
├── LICENSE
└── README.md
```

---

## 📄 License & Maintainer
Maintained by **[MD. Mahamudul Hasan](https://github.com/MHMITHUN)**  
- Email: mhmmithun1@gmail.com | Phone: 01560027539  
- GitHub: [@MHMITHUN](https://github.com/MHMITHUN) | LinkedIn: [linkedin.com/in/mahamudul0](https://linkedin.com/in/mahamudul0)  
Released under the [MIT License](LICENSE).

