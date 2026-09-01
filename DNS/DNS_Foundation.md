
📝 DNS Foundation — Before Record Types
1. DNS
DNS = Domain Name System
Its basic job is to translate a human-readable name into an address that computers can use.
google.com
    ↓
   DNS
    ↓
IP address


2. DNS Resolver
Your computer normally asks a DNS resolver to find the answer.
Computer
   ↓
DNS Resolver
   ↓
IP address

The resolver does the searching on your behalf.

3. DNS Cache
The resolver may already have the answer stored:
google.com → IP

If the cached answer is still valid, it can return it without searching the DNS hierarchy again.

4. TTL — Time To Live
DNS answers aren't cached forever.
Record
  ↓
TTL = 300 seconds
  ↓
Can be cached for that period

After the cached information expires, the resolver needs to obtain fresh information.

5. DNS Hierarchy
DNS is organized like a tree:
.
↓
.com
↓
example.com
↓
www.example.com

. = Root
.com = TLD (Top-Level Domain)
example.com = domain
www = label/subdomain portion

6. Authoritative DNS Server
An authoritative DNS server provides the authoritative DNS information for a particular zone.
Think:
Resolver
   ↓
Who handles example.com?
   ↓
Authoritative DNS server
   ↓
DNS records


7. Recursive vs Iterative
Recursive:
Computer → Resolver
"Find the answer for me."

The resolver is responsible for finding the final answer.
Iterative:
Resolver → Root
"Where should I look?"

Root → ".com"

Resolver → .com
"Where should I look?"

.com → "Ask example.com's authoritative server."

So remember:
Recursive = find the answer for me.
 Iterative = tell me where to look next.

8. DNS Zone
A DNS zone is the portion of the DNS namespace managed by a particular DNS authority.
A zone contains DNS records:
company.com
     ↓
DNS Zone
     ↓
DNS Records

A domain and a zone can look similar, but they aren't necessarily the same thing.

🧠 The master mental picture
Whenever you see DNS, remember this first:
                DNS
                  │
                  ▼
              Resolver
                  │
            ┌─────┴─────┐
            │           │
          Cache       No Cache
            │           │
            │           ▼
            │       DNS Hierarchy
            │           │
            │           ▼
            │          Root
            │           ↓
            │          TLD
            │           ↓
            │    Authoritative DNS
            │           ↓
            │       DNS Zone
            │           ↓
            │      DNS Record
            │           │
            └───────┬───┘
                    ▼
                 Answer

This is our foundation.
From here, we'll build the actual record types:
A → IPv4
AAAA → IPv6
CNAME → another DNS name
MX → mail server
NS → authoritative name servers
TXT → text/policy/verification
...

And when we eventually reach Linux dig, AWS Route 53, Docker, Kubernetes and Terraform, we'll keep coming back to this foundation.
