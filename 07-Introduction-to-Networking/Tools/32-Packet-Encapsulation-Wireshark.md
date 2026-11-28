# Encapsulation, decapsulation and Wireshark packet analysis

Encapsulation wraps upper-layer content in protocol control information; decapsulation interprets/removes relevant framing. Not every layer adds a separate header. Typical Ethernet II IPv4 TCP:
```text
[Ethernet 14][IPv4 20+][TCP 20+][Application bytes][FCS 4]
```
With 100 bytes, no options/tags/TLS, frame is 158 bytes including FCS; capture often 154. UDP equivalent 146. Physical preamble/SFD/interframe gap excluded. TCP/IPv4 over 1500-byte MTU has illustrative MSS 1460; IPv6 1440 with base headers. Actual segmentation, padding, VLAN, TLS, tunnel overhead and offloads may change sizes. IP addresses normally persist through routers (except NAT/middleboxes); local link framing is rebuilt and TTL/Hop Limit decremented.

```mermaid
flowchart LR
    D[Application bytes] --> T[Transport segment]
    T --> I[IP packet]
    I --> E[Ethernet frame]
    E --> B[Signals]
```
Wireshark is free/open-source. [Official downloads](https://www.wireshark.org/download.html), [user guide](https://www.wireshark.org/docs/wsug_html/), [TShark manual](https://www.wireshark.org/docs/man-pages/tshark.html). Windows: official signed installer, Npcap only when needed for authorized live captures; offline file analysis does not require capturing. Ubuntu: sudo apt update; sudo apt install wireshark tshark (optional); configure approved capture permissions per distro, do not run GUI as root. WSL2 sees its own interfaces. Existing guide: [topology and Wireshark](19-Topology-Documentation-Wireshark.md).

Open an approved existing capture, select packet, expand Ethernet/IP/TCP or UDP and application details, compare raw bytes. Packet list is summary; protocol tree dissection; byte view raw representation. Frame item often contains capture metadata rather than a separate wire header. Display filters: eth, ip, ipv6, tcp, udp, icmp, icmpv6, tcp.port == 443, tcp.flags.syn == 1 && tcp.flags.ack == 0, http, tls. Display vs capture filter syntax differs. TShark offline reference:
```bash
tshark -r sample.pcapng -Y "tcp" -V
```
-r selects file, -Y display filter, -V verbose details. sample.pcapng illustrative; no file supplied. No capture or installation performed. Troubleshooting: wrong interface/vantage, TLS-encrypted payload, missing FCS, offload-generated checksum warnings, truncated captures. Avoid publishing credentials, cookies, private addresses or student data. UniTrack owned development traffic can be inspected when authorized; ProTrack-MX cloud traffic may be encrypted and requires approved test context. Network visibility is not permission to inspect third-party traffic.
