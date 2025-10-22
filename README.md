## Team
- **Member 1 — Victor Hurst** — victor.hurst@mail.utoronto.ca
- **Member 2 — Janani Gurram** — j.gurram@mail.utoronto.ca

## Contributions (per member)
- **Victor Hurst**
  - Tasks: Handling the ARP Cache, IP Forwarding, Creating The Frames (Ethernet, IP,  ARP Requests etc.), ICMP Messages, ARP Replies and Requests, Handling IP Requests
- **Member 2**
  - Tasks: All Of Testing, ARP Cache,  IP Forwarding, ICMP Messages, ARP Replies and Requests
 
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
 
## Testing

Notes: After pulling the code, you may need to give executable permission to files and recreate auth_key and pox symbolic links.

We tested by running the following commands (on the mininet cli) using our router and comparing it to the sample solution router using wireshark. We ensured that all commands were working as expected and the same requests/responses were sent by our solution and the sample.
1) Ping from client to router interfaces
   - `client ping -c 3 192.168.2.1`
   - `client ping -c 3 172.64.3.1`
   - `client ping -c 3 10.0.1.1`
   - What requirements this tests: ICMP echo replies to router interface addresses demonstrate the router correctly recognizes and answers ICMP destined to itself (requirement: ICMP echo reply).

2) Traceroute from client to router interfaces
   - `client traceroute -n 192.168.2.1`
   - `client traceroute -n 172.64.3.1`
   - `client traceroute -n 10.0.1.1`
   - What requirements this tests: traceroute uses ICMP (and TTL expiration); correct intermediate behavior indicates router decrements TTL, generates ICMP Time Exceeded when appropriate, and participates correctly in forwarding/traceroutes.

3) Ping from client to application servers
   - `client ping -c 3 192.168.2.2`
   - `client ping -c 3 172.64.3.10`
   - What requirements this tests: demonstrates the forwarding path (longest-prefix route lookup, ARP resolution, setting correct destination MAC, and forwarding) works from client → server.

4) Traceroute from client to application servers
   - `client traceroute -n 192.168.2.2`
   - `client traceroute -n 172.64.3.10`
   - What requirements this tests: traceroutes that reach the servers confirm that IP forwarding and TTL/ICMP behavior across hops are correct.

5) HTTP download from application servers
   - `client wget http://192.168.2.2`
   - `client wget http://172.64.3.10`
   - What requirements this tests: demonstrating a successful HTTP request/response proves forwarding of TCP and correct two-way connectivity between client and server.
