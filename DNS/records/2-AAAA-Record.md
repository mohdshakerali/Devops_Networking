AAAA Record — From Scratch
You already know:
A record
Hostname → IPv4

Now AAAA is almost the same thing, except it maps a hostname to an IPv6 address.

1. The basic structure
Suppose we have:
app.company.com    AAAA    2001:db8:1234::10

Break it down:
app.company.com
       │
      AAAA
       │
       ▼
2001:db8:1234::10

app.company.com → hostname
AAAA → record type
2001:db8:1234::10 → IPv6 address
So:
AAAA record = hostname → IPv6 address

2. Compare it directly with A
This is the easiest way to remember it:
A:
app.company.com
       ↓
192.168.1.10
       IPv4

AAAA:
app.company.com
       ↓
2001:db8::10
       IPv6

Same hostname can have both:
app.company.com
       │
       ├── A ────→ 192.168.1.10
       │            IPv4
       │
       └── AAAA ─→ 2001:db8::10
                    IPv6


3. Why does AAAA exist?
You already learned that IPv4 addresses are limited.
IPv6 provides a vastly larger address space.
So a server/application can be reachable using IPv6:
Client
   │
   │ IPv6
   ▼
2001:db8::10

DNS needs a way to say:
"app.company.com is reachable at this IPv6 address."
That's what the AAAA record does.

4. Google example
When your computer wants to access:
google.com

it can ask the DNS resolver for:
AAAA google.com

Meaning:
"Give me the IPv6 address(es) associated with google.com."
If Google's DNS has AAAA records, the resolver can return IPv6 addresses.
Similarly:
A google.com

means:
"Give me the IPv4 address(es) associated with google.com."
So the queries are different:
A     → "Give me IPv4"
AAAA  → "Give me IPv6"


5. Multiple AAAA records are also possible
Just like A records:
app.company.com    AAAA    2001:db8::10
app.company.com    AAAA    2001:db8::11

One hostname can therefore have:
               app.company.com
                       │
             ┌─────────┴─────────┐
             ↓                   ↓
       2001:db8::10        2001:db8::11
          IPv6                 IPv6


🧠 Your mental model
Don't overcomplicate AAAA.
Just remember:
A
│
└── Hostname → IPv4


AAAA
│
└── Hostname → IPv6

A = IPv4
AAAA = IPv6
That's the core.

🧪 Test — AAAA Question 1
You see:
api.company.com    AAAA    2001:db8::25

What does this mean?
A) api.company.com is an IPv6 address.
B) 2001:db8::25 is the IPv6 address associated with api.company.com.
C) 2001:db8::25 is an IPv4 address.
D) The server is guaranteed to be reachable over IPv6.
Your answer?

