# Wireshark Display Filter Cheat Sheet for ISP & Network Engineers

| Troubleshooting Scenario | Wireshark Display Filter |
|---|---|
| **Packet Loss & Retransmissions** | `tcp.analysis.retransmission || tcp.analysis.duplicate_ack` |
| **TCP Zero Window (Server Overload)** | `tcp.analysis.zero_window` |
| **DNS Server Errors & Timeouts** | `dns.flags.rcode != 0 || dns.time > 1.0` |
| **DHCP Discovery & Leases** | `bootp` or `dhcp` |
| **Rogue DHCP Detection** | `bootp.option.type == 53 && bootp.option.dhcp == 2 && ip.src != 192.168.30.10` |
| **ARP Poisoning / Conflicts** | `arp.duplicate-address-detected || arp.opcode == 2` |
| **Broadcast Storms** | `eth.dst == ff:ff:ff:ff:ff:ff` |
| **Slow HTTP Responses** | `http.time > 2.0` |
| **MTU / ICMP Fragmentation Needed** | `icmp.type == 3 && icmp.code == 4` |

