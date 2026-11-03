# Root Cause Analysis (RCA): PPPoE MTU Blackhole & TCP Stalling

| Incident ID | Severity | Date | Author / SQA | Status |
|---|---|---|---|---|
| **INC-2026-119** | P1 - Critical | Oct 24, 2026 | Md. Mahamudul Hasan | **RESOLVED & VERIFIED** |

---

## 1. Problem Description
Multiple FTTH broadband users in Gazipur reported that ICMP `ping` and WhatsApp messaging functioned normally, but large web pages (banking portals, video streaming, e-commerce) failed to load and stalled with `ERR_CONNECTION_TIMED_OUT`.

## 2. Packet Analysis via Wireshark
Filter applied: `tcp.analysis.retransmission || icmp.type == 3`

### Packet Capture Findings
1. Client established TCP 3-way handshake successfully (`SYN`, `SYN-ACK`, `ACK`).
2. Client requested HTTPS TLS Client Hello (packet size 517 bytes) - Successful.
3. Server replied with Server Hello and TLS Certificate (packet size 1514 bytes).
4. Broadband PPPoE encapsulation added 8 bytes of PPP/PPPoE headers, raising frame size to 1522 bytes, exceeding the Ethernet MTU of 1500 bytes.
5. The gateway attempted to fragment, but the packet had the `DF (Don't Fragment)` bit set.
6. The router dropped the packet and emitted `ICMP Type 3, Code 4 (Fragmentation Needed and DF set)`.
7. Because an upstream edge firewall blocked all ICMP packets indiscriminately, the client never received the MTU reduction notice, resulting in continuous retransmissions until connection timeout.

## 3. Remediation & Action Taken
Configured MikroTik RouterOS MSS clamping on PPPoE profiles:
```bash
/ip firewall mangle
add action=change-mss chain=forward new-mss=clamp-to-pmtu passthrough=yes protocol=tcp tcp-flags=syn
```
Clamped Maximum Segment Size (MSS) to 1440 bytes automatically, eliminating the blackhole across all PPPoE clients.

