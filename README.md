## Team
- **Member 1 — Victor Hurst** — email: victor.hurst@mail.utoronto.ca
- **Member 2 — Full Name** — Role(s)

## Contributions (per member)
- **Victor Hurst**
  - Tasks: Handling the ARP Cache, IP Forwarding, Creating The Frames (Ethernet, IP,  ARP Requests etc.), ICMP Messages, ARP Replies and Requests, Handling IP Requests
- **Member 2**
  - Tasks: Most of the testing, ARP Cache,  IP Forwarding, ICMP Messages, ICMP Messages
 
## Implemented functions / files

### `sr_handlepacket`
- **Documentation:**
  - Handles an incoming Ethernet packet. Determines if the packet is ARP or IP
    and calls the corresponding handler. Logs unknown packet types for debugging.
- **Arguments:**
  - `sr` – Pointer to the router instance
  - `packet` – Pointer to the raw packet data (`uint8_t *`)
  - `len` – Length of the packet in bytes (`unsigned int`)
  - `interface` – Name of the interface the packet was received on (`char *`)

### `sr_arpcache_sweepreqs`
- **Documentation:**
  - Periodically traverses the ARP request queue. For each request:
    - If it has timed out and max retries are not reached, resend the ARP request.
    - If max retries have been reached, send ICMP host unreachable to all packets
      waiting on this request and remove it from the cache.
- **Arguments:**
  - `sr` – Pointer to the router instance
