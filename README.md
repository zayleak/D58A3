## Team
- **Member 1 — Victor Hurst** — victor.hurst@mail.utoronto.ca
- **Member 2 — Full Name** — Role(s)

## Contributions (per member)
- **Victor Hurst**
  - Tasks: Handling the ARP Cache, IP Forwarding, Creating The Frames (Ethernet, IP,  ARP Requests etc.), ICMP Messages, ARP Replies and Requests, Handling IP Requests
- **Member 2**
  - Tasks: All Of Testing, ARP Cache,  IP Forwarding, ICMP Messages, ICMP Messages
 
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
  
### `handle_arp_packet`
- **Documentation:**
  - Processes ARP packets. Sends replies for requests to the router and updates the ARP cache for replies.
- **Arguments:**
  - `sr` – Router instance
  - `packet` – Raw packet data (`uint8_t *`)
  - `len` – Packet length (`unsigned int`)
  - `interface` – Interface name (`char *`)

### `forward_ip_packet`
- **Documentation:**
  - Forwards IP packets toward their next hop, handling TTL expiration, routing decisions, and ARP resolution.
- **Arguments:**
  - `sr` – Router instance
  - `packet` – Raw packet data (`uint8_t *`)
  - `len` – Packet length (`unsigned int`)
  - `interface` – Incoming interface name (`char *`)
  - `ip_hdr` – Pointer to the IP header (`sr_ip_hdr_t *`)

### `handle_ip_packet`
- **Documentation:**
  - Handles IP packets destined for the router or forwards them otherwise. Responds to ICMP Echo Requests and sends appropriate ICMP errors for TCP/UDP packets.
- **Arguments:**
  - `sr` – Router instance
  - `packet` – Raw packet data (`uint8_t *`)
  - `len` – Packet length (`unsigned int`)
  - `interface` – Incoming interface name (`char *`)

### `handle_arp_reply`
- **Documentation:**
  - Updates the ARP cache with incoming replies and processes any queued packets waiting for that ARP resolution.
- **Arguments:**
  - `sr` – Router instance
  - `arp_hdr` – ARP header (`struct sr_arp_hdr *`)
