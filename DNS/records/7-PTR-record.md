Let's continue with **PTR** properly.

### PTR — Pointer Record

**PTR** stands for **Pointer Record**.

A PTR record is a **DNS record used for reverse DNS lookup**.

So far, you've learned that an **A record** does this:

```text
Hostname → IPv4 address
```

For example:

```text
web.example.com → 192.168.1.50
```

A **PTR record works in the opposite direction**:

```text
IPv4 address → Hostname
```

For example:

```text
192.168.1.50 → web.example.com
```

That's the main thing you need to remember:

> **A = forward lookup**
> **PTR = reverse lookup**

---

### How does PTR actually work?

Suppose you have this server:

```text
IP address: 192.168.1.50
Hostname:   web01.example.com
```

When you want to find the IP of `web01.example.com`, you perform a normal DNS lookup:

```bash
dig web01.example.com
```

The DNS server can return:

```text
web01.example.com → 192.168.1.50
```

That's the **A record**.

But now suppose your logs show only:

```text
192.168.1.50
```

and you want to know:

> "What hostname is associated with this IP?"

You can perform a reverse DNS lookup:

```bash
dig -x 192.168.1.50
```

The `-x` means **perform a reverse lookup**.

You might get:

```text
50.1.168.192.in-addr.arpa.  IN  PTR  web01.example.com.
```

Notice something strange:

```text
192.168.1.50
```

became:

```text
50.1.168.192.in-addr.arpa
```

The IPv4 octets are **reversed**.

This is because IPv4 reverse DNS uses the special DNS domain:

```text
in-addr.arpa
```

So:

```text
192.168.1.50
```

is represented in reverse DNS as:

```text
50.1.168.192.in-addr.arpa
```

And the PTR record says:

```text
50.1.168.192.in-addr.arpa
        ↓
web01.example.com
```

Therefore:

```text
192.168.1.50
        ↓
PTR lookup
        ↓
web01.example.com
```

---

### One important thing

**PTR does not mean that every IP address must have a hostname.**

An IP address may have:

```text
PTR → hostname
```

or it may have **no PTR record at all**.

For example:

```bash
dig -x 192.168.1.50
```

could return a hostname.

But:

```bash
dig -x 192.168.1.60
```

might return:

```text
NXDOMAIN
```

**NXDOMAIN** = **Non-Existent Domain**

That means DNS is saying that the requested reverse-DNS name does not exist.

---

### Who controls the PTR?

This is an important DevOps concept.

For normal DNS records such as:

```text
example.com → 1.2.3.4
```

the domain owner normally manages the authoritative DNS zone.

But PTR records are associated with **IP address ownership/control**.

For example, if a cloud provider gives you:

```text
203.0.113.50
```

the reverse DNS for that IP is generally controlled by the organization that manages that IP block, such as the cloud provider or ISP. They may provide a way for you to configure the PTR.

That's why you can't simply create:

```text
203.0.113.50 → myserver.example.com
```

inside your normal `example.com` DNS zone and expect reverse DNS to work.

The reverse DNS namespace is separate:

```text
50.113.0.203.in-addr.arpa
```

---

### Why do we care about PTR in DevOps?

Imagine you're checking a server log and you see:

```text
Connection received from 10.20.30.40
```

You can try:

```bash
dig -x 10.20.30.40
```

and potentially get:

```text
db01.internal.example.com
```

Now you know that:

```text
10.20.30.40
        ↓
db01.internal.example.com
```

This can be useful when investigating:

* server logs
* network connections
* mail servers
* monitoring systems
* security logs
* infrastructure problems

Another common place where PTR matters is **email infrastructure**. Mail systems often perform reverse DNS checks, so a poorly configured or missing PTR can contribute to email-delivery/reputation problems.

---

### One more command you'll use

You can also use:

```bash
host 192.168.1.50
```

which can perform reverse DNS lookup.

But since you're learning DNS for DevOps, I want you to become comfortable with:

```bash
dig -x 192.168.1.50
```

because you've already been using `dig`.

So the complete picture is now:

```text
A record
hostname → IPv4

PTR record
IPv4 → hostname
```

And for IPv4 reverse DNS:

```text
192.168.1.50
      ↓
50.1.168.192.in-addr.arpa
      ↓
PTR
      ↓
web01.example.com
```

**That's PTR.** We won't move to SOA until you're comfortable with this one.
