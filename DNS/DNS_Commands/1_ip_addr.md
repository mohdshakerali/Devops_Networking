
# Linux Networking — `ip addr` Command

## 1. What is `ip addr`?

`ip addr` is a Linux command used to see:

* Network interfaces available on the machine
* Whether an interface is UP or DOWN
* IPv4 addresses assigned to interfaces
* IPv6 addresses assigned to interfaces
* Subnet/prefix information
* Broadcast address
* Other basic interface information

Command:

```bash
ip addr
```

Short form:

```bash
ip a
```

For DevOps, `ip addr` is mainly useful for answering:

> **What network interfaces does this machine have, and what IP addresses are assigned to them?**

---

# 2. Example Output

A simplified example:

```text
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 state UNKNOWN
    link/loopback 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo

2: eno1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 state DOWN
    link/ether 11:22:33:44:55:66
     
3: wlo1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 state UP
    link/ether 22:33:44:55:66:77
    inet 192.168.10.25/24 brd 192.168.10.255 scope global dynamic wlo1
    inet6 2001:db8:1234::25/64 scope global
    inet6 fe80::1234:5678:abcd:ef01/64 scope link
```

We will understand this step by step.

---

# 3. Network Interface

An **interface** is the network connection through which the computer communicates.

Examples:

```text
lo
eno1
wlo1
```

Common examples:

| Interface | Usually means       |
| --------- | ------------------- |
| `lo`      | Loopback            |
| `eno1`    | Ethernet/Wired      |
| `wlo1`    | Wi-Fi/Wireless      |
| `docker0` | Docker bridge       |
| `br-*`    | Linux/Docker bridge |
| `veth*`   | Virtual Ethernet    |

You do not need to memorize every possible interface name.

For normal DevOps troubleshooting, first identify:

> **Which interface is actually being used?**

---

# 4. `lo` — Loopback

Example:

```text
1: lo: <LOOPBACK,UP,LOWER_UP>
    inet 127.0.0.1/8 scope host lo
```

`lo` = **Loopback interface**

The important address is:

```text
127.0.0.1
```

`127.0.0.1` means:

> **This same machine.**

For example:

```bash
ping 127.0.0.1
```

This tests communication with the local machine through the loopback interface.

In DevOps, you may see applications communicating locally:

```text
Application A
     ↓
127.0.0.1:8080
     ↓
Application B
```

Remember:

```text
lo       = loopback interface
127.0.0.1 = this machine
```

---

# 5. Ethernet Interface

Example:

```text
2: eno1: <NO-CARRIER,BROADCAST,MULTICAST,UP>
    ...
    state DOWN
```

`eno1` is an Ethernet/wired network interface.

The important part here is:

```text
NO-CARRIER
state DOWN
```

This indicates that the interface currently does not have an active physical network connection.

For example, if the Ethernet cable is not connected, you may see:

```text
NO-CARRIER
state DOWN
```

For DevOps troubleshooting:

```text
ip addr
```

can quickly tell you whether the expected network interface is active.

---

# 6. Wi-Fi Interface

Example:

```text
3: wlo1: <BROADCAST,MULTICAST,UP,LOWER_UP>
    ...
    state UP
```

`wlo1` is a Wi-Fi/wireless interface.

Here:

```text
state UP
```

means the interface is active.

We then look for an IPv4 address:

```text
inet 192.168.10.25/24
```

Therefore:

```text
wlo1
   ↓
192.168.10.25/24
```

This tells us that the Wi-Fi interface has an IPv4 address.

---

# 7. Understanding `inet`

Example:

```text
inet 192.168.10.25/24
```

`inet` indicates an **IPv4 address**.

So:

```text
192.168.10.25
```

is the IPv4 address.

And:

```text
/24
```

is the prefix length.

You already learned IPv4 and subnetting, so:

```text
/24
```

means:

```text
Network bits = 24
Host bits    = 8
```

The equivalent subnet mask is:

```text
255.255.255.0
```

---

# 8. Finding the Network

Suppose we have:

```text
192.168.10.25/24
```

The subnet mask is:

```text
255.255.255.0
```

Therefore:

```text
Network address = 192.168.10.0
```

So the machine belongs to:

```text
192.168.10.0/24
```

The usable host range is:

```text
192.168.10.1
        ↓
192.168.10.254
```

And the broadcast address is:

```text
192.168.10.255
```

This connects directly with the IPv4/subnetting concepts you already learned.

---

# 9. `brd` — Broadcast Address

Example:

```text
inet 192.168.10.25/24 brd 192.168.10.255
```

`brd` = **broadcast**

So:

```text
brd 192.168.10.255
```

means the broadcast address for this network is:

```text
192.168.10.255
```

For a `/24` network:

```text
192.168.10.0/24

Network   → 192.168.10.0
Host      → 192.168.10.1 - 192.168.10.254
Broadcast → 192.168.10.255
```

---

# 10. `scope host`

Example:

```text
inet 127.0.0.1/8 scope host lo
```

`scope host` means the address is only relevant to the local machine.

The common example is:

```text
127.0.0.1
```

So:

```text
127.0.0.1
   ↓
This machine only
```

---

# 11. `scope global`

Example:

```text
inet 192.168.10.25/24 scope global
```

For our DevOps purposes, `scope global` means the address is not restricted to the local host like `127.0.0.1`.

It can be used for normal network communication through the interface.

So:

```text
127.0.0.1
scope host
```

versus:

```text
192.168.10.25
scope global
```

---

# 12. `dynamic`

Example:

```text
scope global dynamic
```

`dynamic` means the IP address was dynamically assigned.

A common way this happens is through:

**DHCP = Dynamic Host Configuration Protocol**

For example:

```text
DHCP
  ↓
192.168.10.25
```

The machine received its address automatically instead of an administrator manually configuring that particular address.

---

# 13. IPv6 — Don't worry about it yet

You may see:

```text
inet6 2001:db8:1234::25/64
```

and:

```text
inet6 fe80::1234:5678:abcd:ef01/64
```

These are IPv6 addresses.

For now, since our goal is **practical DevOps networking**, you don't need to understand every IPv6 detail.

Just remember:

```text
inet  → IPv4
inet6 → IPv6
```

We'll deal with IPv6 when it becomes relevant.

---

# 14. `UP` vs `DOWN`

This is very important for troubleshooting.

### UP

Example:

```text
wlo1: <...UP,LOWER_UP...>
state UP
```

The interface is active.

### DOWN

Example:

```text
eno1: <NO-CARRIER,...>
state DOWN
```

The interface isn't currently providing an active connection.

---

# 15. Important: UP does NOT mean Internet is working

This is one of the most important things to remember.

Suppose:

```text
wlo1
state UP
inet 192.168.10.25/24
```

This tells us:

> The Wi-Fi interface is active and has an IPv4 address.

It does **NOT** prove:

> The Internet is working.

There are still other things that could be wrong:

```text
Interface
    ↓
IP address
    ↓
Routing
    ↓
Gateway
    ↓
DNS
    ↓
Remote server
```

We use different commands to test these different layers.

---

# 16. DevOps Use of `ip addr`

Suppose someone tells you:

> "This Linux server cannot connect to anything."

Your first check can be:

```bash
ip addr
```

You want to determine:

### Question 1

Is the expected interface present?

```text
eth0
ens3
wlo1
etc.
```

### Question 2

Is the interface UP?

```text
state UP
```

### Question 3

Does it have an IP address?

Look for:

```text
inet x.x.x.x/prefix
```

### Question 4

What network is the IP part of?

For example:

```text
192.168.10.25/24
```

means:

```text
Network = 192.168.10.0/24
```

Once you've answered these questions, you move to the next command:

```bash
ip route
```

because `ip addr` tells us **what IP/interface we have**, while `ip route` tells us **where Linux will send packets**.

---

# 17. Quick Reference

```bash
# Show network interfaces and IP addresses
ip addr

# Short version
ip a
```

Look for:

```text
Interface name
       ↓
state UP/DOWN
       ↓
inet = IPv4
       ↓
inet6 = IPv6
       ↓
/24 = prefix length
       ↓
brd = broadcast address
```

Example:

```text
wlo1
  ↓
state UP
  ↓
inet 192.168.10.25/24
  ↓
IPv4 = 192.168.10.25
  ↓
Prefix = /24
  ↓
Mask = 255.255.255.0
  ↓
Network = 192.168.10.0
  ↓
Broadcast = 192.168.10.255
```

---

# 🧠 One-line memory

> **`ip addr` = "What network interfaces do I have, are they active, and what IP addresses are assigned to them?"**

### DevOps troubleshooting sequence

```text
ip addr
   ↓
Do I have the expected interface?
   ↓
Is it UP?
   ↓
Does it have an IP?
   ↓
What network am I on?
   ↓
ip route
```

**Next command to learn:** `ip route` — this will tell us about the **default gateway and how Linux decides where to send packets**.