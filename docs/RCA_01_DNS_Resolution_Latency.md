# Root Cause Analysis (RCA): Intermittent DNS Resolution Latency & SERVFAIL

| Incident ID | Severity | Date | Author / SQA | Status |
|---|---|---|---|---|
| **INC-2026-104** | P2 - Major | Oct 18, 2026 | Md. Mahamudul Hasan | **RESOLVED & VERIFIED** |

---

## 1. Executive Summary
Between 09:30 AM and 11:15 AM, 40+ corporate workstations experienced sporadic 5-second delays opening cloud CRM and internal web apps. Standard ping to the default gateway (192.168.20.1) displayed steady 1ms response, ruling out physical fiber or cable faults.

## 2. Investigation & Wireshark Packet Forensics
Wireshark was placed on a mirrored SPAN port on `SW-CORE-L3` capturing traffic filtered by `udp.port == 53`.

### Key Evidence
1. **Packet #1402**: Client `192.168.20.50` sent standard query `A crm.asiannet.internal`.
2. **Packet #1403**: Primary DNS server `192.168.30.10` did not reply for 4.89 seconds.
3. **Packet #1820**: Secondary query redirected to external `8.8.8.8` which responded with `RCODE: NXDOMAIN` for internal private zone.

## 3. Root Cause
The BIND9 recursive query queue exhausted its worker threads due to a recursive loop when resolving external upstream queries while simultaneously serving internal zones.

## 4. Remediation & QA Test Verification
1. Modified `/etc/bind/named.conf.options`:
   ```bash
   forwarders { 1.1.1.1; 8.8.8.8; };
   forward only;
   max-cache-size 512m;
   ```
2. Restarted service: `systemctl restart bind9`.
3. Verified via Python automated dig loop: 1,000 queries sent across 10 concurrent threads with **0% failure** and average latency **1.4ms**.

