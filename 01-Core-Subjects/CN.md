# 🌐 CN — Computer Networks

> Networking fundamentals are essential for backend, distributed systems, and full-stack roles.

---

## 📖 Key Topics Overview

| Topic | Interview Importance |
|-------|---------------------|
| OSI Model | ⭐⭐⭐⭐⭐ |
| TCP vs UDP | ⭐⭐⭐⭐⭐ |
| HTTP/HTTPS | ⭐⭐⭐⭐⭐ |
| DNS | ⭐⭐⭐⭐ |
| IP Addressing | ⭐⭐⭐ |
| TCP 3-way Handshake | ⭐⭐⭐⭐ |
| REST vs WebSocket | ⭐⭐⭐⭐ |

---

## 1. OSI Model (7 Layers)

| Layer | Name | Protocol/Technology | Mnemonic |
|-------|------|---------------------|---------|
| 7 | **Application** | HTTP, HTTPS, FTP, SMTP, DNS | **A**ll |
| 6 | **Presentation** | SSL/TLS, JPEG, ASCII | **P**eople |
| 5 | **Session** | NetBIOS, RPC | **S**eem |
| 4 | **Transport** | TCP, UDP | **T**o |
| 3 | **Network** | IP, ICMP, Routers | **N**eed |
| 2 | **Data Link** | Ethernet, MAC, Switches | **D**ata |
| 1 | **Physical** | Cables, Signals, NIC | **P**rocessing |

**Mnemonic:** "All People Seem To Need Data Processing"

**Key responsibilities:**
- **Transport Layer (4)**: End-to-end communication, TCP/UDP, port numbers
- **Network Layer (3)**: Routing, IP addresses, packet forwarding
- **Data Link Layer (2)**: MAC addresses, frame delivery on same network

**TCP/IP Model (4 layers):**
```
Application (HTTP, DNS, SMTP)
Transport (TCP, UDP)
Internet (IP, ICMP)
Network Access (Ethernet, WiFi)
```

---

## 2. TCP vs UDP

| Feature | TCP | UDP |
|---------|-----|-----|
| **Type** | Connection-oriented | Connectionless |
| **Reliability** | Guaranteed delivery | No guarantee |
| **Order** | In-order delivery | May arrive out of order |
| **Error checking** | Yes (checksums + ACK) | Checksum only |
| **Speed** | Slower | Faster |
| **Header size** | 20 bytes | 8 bytes |
| **Flow control** | Yes (sliding window) | No |
| **Use cases** | HTTP, HTTPS, FTP, Email | Video streaming, DNS, Gaming, VoIP |

**Interview one-liner:** "Use TCP when you can't afford to lose data. Use UDP when you can't afford delay."

---

## 3. TCP 3-Way Handshake

```
Client                          Server
  |                               |
  |—— SYN (seq=x) ————————————→ |   Step 1: Client initiates
  |                               |
  |← SYN-ACK (seq=y, ack=x+1) —|   Step 2: Server acknowledges
  |                               |
  |—— ACK (ack=y+1) ————————→   |   Step 3: Client confirms
  |                               |
  |====== Connection Established =|
```

**4-Way Handshake (Connection Termination):**
```
Client —→ FIN
Server ←— ACK
Server —→ FIN
Client ←— ACK
```

---

## 4. HTTP/HTTPS

### HTTP Methods

| Method | Purpose | Idempotent? | Safe? |
|--------|---------|-------------|-------|
| GET | Retrieve resource | Yes | Yes |
| POST | Create resource | No | No |
| PUT | Update (full replace) | Yes | No |
| PATCH | Update (partial) | No | No |
| DELETE | Remove resource | Yes | No |
| HEAD | Get headers only | Yes | Yes |

### HTTP Status Codes

| Range | Meaning | Examples |
|-------|---------|---------|
| 1xx | Informational | 100 Continue |
| 2xx | Success | 200 OK, 201 Created, 204 No Content |
| 3xx | Redirection | 301 Moved Permanently, 302 Found |
| 4xx | Client Error | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found |
| 5xx | Server Error | 500 Internal Server Error, 503 Service Unavailable |

### HTTP vs HTTPS

| | HTTP | HTTPS |
|--|------|-------|
| Encryption | None | TLS/SSL encrypted |
| Port | 80 | 443 |
| Security | Vulnerable to MITM | Secure |
| Certificate | Not required | SSL certificate required |

### TLS Handshake (Simplified)
1. Client → Server: "Hello, I support TLS 1.3"
2. Server → Client: Certificate + Public Key
3. Client: Verifies certificate with CA
4. Client → Server: Session key (encrypted with server's public key)
5. Both: Use session key for symmetric encryption

---

## 5. DNS (Domain Name System)

**What it does:** Translates human-readable domain names → IP addresses

### DNS Resolution Process
```
User types: www.google.com
    ↓
Browser Cache → OS Cache → Router Cache → ISP DNS Resolver
    ↓ (if not cached)
Root DNS Server → TLD Server (.com) → Authoritative DNS Server
    ↓
Returns: 142.250.x.x
```

**DNS Record Types:**

| Record | Purpose | Example |
|--------|---------|---------|
| **A** | Domain → IPv4 | google.com → 142.250.1.1 |
| **AAAA** | Domain → IPv6 | — |
| **CNAME** | Alias to another domain | www → google.com |
| **MX** | Mail server | google.com → mail.google.com |
| **TXT** | Text info (used for verification) | SPF, DKIM |
| **NS** | Name servers for domain | — |

---

## 6. IP Addressing

### IPv4 vs IPv6

| Feature | IPv4 | IPv6 |
|---------|------|------|
| Size | 32 bits | 128 bits |
| Addresses | ~4.3 billion | ~340 undecillion |
| Format | 192.168.1.1 | 2001:db8::1 |
| NAT required | Yes (address exhaustion) | No |

### Subnetting
CIDR notation: `192.168.1.0/24` means first 24 bits are network, last 8 are host.
- `/24` → 256 addresses (254 usable)
- `/16` → 65,536 addresses

### Private IP Ranges
- `10.0.0.0/8`
- `172.16.0.0/12`
- `192.168.0.0/16`

---

## 7. Application Layer Protocols

| Protocol | Port | Use |
|----------|------|-----|
| HTTP | 80 | Web |
| HTTPS | 443 | Secure web |
| FTP | 21 | File transfer |
| SSH | 22 | Secure shell |
| SMTP | 25 | Email sending |
| POP3 | 110 | Email retrieval |
| IMAP | 143 | Email access |
| DNS | 53 | Name resolution |
| DHCP | 67/68 | IP assignment |

---

## 8. REST vs WebSocket vs GraphQL

| | REST | WebSocket | GraphQL |
|--|------|-----------|---------|
| Communication | Request-Response | Full-duplex, persistent | Request-Response |
| Protocol | HTTP | ws:// or wss:// | HTTP |
| Real-time | No | Yes | Subscriptions |
| Use case | CRUD APIs | Chat, live feeds | Flexible data queries |
| Stateless | Yes | No | Yes |

### WebSocket Flow
```
HTTP Upgrade Request → Server Accepts → WebSocket Connection
Client ←→ Server (bidirectional, persistent)
```

---

## ❓ Frequently Asked Interview Questions

**Q1: Difference between TCP and UDP?**
> TCP is connection-oriented, reliable, ordered, and slower — used for HTTP, emails. UDP is connectionless, unreliable, faster — used for video streaming, DNS, gaming.

**Q2: What happens when you type google.com in a browser?**
1. DNS resolution (domain → IP)
2. TCP 3-way handshake
3. TLS handshake (for HTTPS)
4. HTTP GET request sent
5. Server responds with HTML
6. Browser parses and renders

**Q3: What is the difference between HTTP and HTTPS?**
> HTTPS adds TLS encryption over HTTP. All data between client and server is encrypted, preventing man-in-the-middle attacks.

**Q4: What is a CDN?**
> Content Delivery Network — geographically distributed servers that cache static content (images, CSS, JS) closer to users, reducing latency.

**Q5: What is the difference between a hub, switch, and router?**
- **Hub**: Broadcasts to all devices (Layer 1)
- **Switch**: Sends to specific device using MAC address (Layer 2)
- **Router**: Routes packets between different networks using IP (Layer 3)

---

## ✅ Revision Checklist

- [ ] Can I name all 7 OSI layers and their responsibilities?
- [ ] Can I explain TCP vs UDP with use cases?
- [ ] Can I draw the TCP 3-way handshake?
- [ ] Do I know all HTTP methods and when to use them?
- [ ] Can I explain the DNS resolution process step by step?
- [ ] Do I know common HTTP status codes?
- [ ] Can I explain what happens when you type a URL in a browser?
- [ ] Do I understand REST vs WebSocket?
