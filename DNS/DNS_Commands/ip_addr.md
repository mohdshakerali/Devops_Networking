Great. This is exactly what I wanted: **your real machine output**. We can learn `ip addr` from it without going into unnecessary networking theory.

# `ip addr` — what you need for DevOps

The command:

```bash
ip addr
```

shows the **network interfaces on your machine and the IP addresses assigned to them**.

Your machine currently shows three interfaces:

```text
1. lo
2. eno1
3. wlo1
```

Let's understand each.

---

# 1. `lo` — Loopback

You have:

```text
1: lo: <LOOPBACK,UP,LOWER_UP>
```

`lo` = **Loopback interface**

And:

```text
inet 127.0.0.1/8
```

The important IP is:

```text
127.0.0.1
```

This means **this same machine**.

For example:

```bash
ping 127.0.0.1
```

You're basically asking your own machine:

> "Can I communicate with myself through the network stack?"

You will commonly see this in DevOps when applications communicate locally.

For example:

```text
Application A
     ↓
127.0.0.1:8080
     ↓
Application B
```

This can mean the application is listening locally on port `8080`.

### What you need to remember

```text
lo       = loopback interface
127.0.0.1 = this machine
```

You don't need to memorize the other `lo` details right now.

---

# 2. `eno1` — Ethernet interface

You have:

```text
2: eno1: <NO-CARRIER,BROADCAST,MULTICAST,UP>
...
state DOWN
```

`eno1` is your **wired Ethernet interface**.

Notice:

```text
NO-CARRIER
state DOWN
```

This basically tells us that the Ethernet interface doesn't currently have an active physical network connection.

Most likely you're not connected through an Ethernet cable right now.

You also have:

```text
altname enp3s0
```

That's simply another name for the same interface.

So:

```text
eno1
enp3s0
```

refer to the same network interface.

### DevOps takeaway

When troubleshooting a Linux server, `ip addr` immediately lets you ask:

> **Is the interface I expect to use actually UP?**

---

# 3. `wlo1` — Your Wi-Fi interface

This is the important one on your machine:

```text
3: wlo1: <BROADCAST,MULTICAST,UP,LOWER_UP>
```

and:

```text
state UP
```

So your Wi-Fi interface is active.

You then have:

```text
inet 192.168.1.8/24
```

This is your **IPv4 address**.

So right now:

```text
Your machine
     |
     └── wlo1
           |
           └── IPv4: 192.168.1.8
```

---

# 4. `/24`

You already know this from IPv4.

```text
192.168.1.8/24
```

means:

```text
Network bits = 24
Host bits    = 8
```

Subnet mask:

```text
255.255.255.0
```

Your network is:

```text
192.168.1.0/24
```

Broadcast:

```text
192.168.1.255
```

And your machine's address is:

```text
192.168.1.8
```

So you're already applying what you learned earlier about IPv4.

---

# 5. `brd 192.168.1.255`

You have:

```text
brd 192.168.1.255
```

`brd` = **broadcast address**

Because your network is:

```text
192.168.1.0/24
```

the broadcast address is:

```text
192.168.1.255
```

You already know why from subnetting.

So there's nothing new to learn here.

---

# 6. `scope global`

You have:

```text
scope global
```

For our DevOps purposes, think:

> This address is usable for communication beyond the local host/interface.

Compare it with:

```text
127.0.0.1
```

which shows:

```text
scope host
```

because `127.0.0.1` is only for this machine.

So:

```text
127.0.0.1 → scope host
192.168.1.8 → scope global
```

---

# 7. `dynamic`

You have:

```text
scope global dynamic
```

This tells us the address was dynamically assigned, typically through **DHCP (Dynamic Host Configuration Protocol)**.

So your Wi-Fi received:

```text
192.168.1.8
```

dynamically rather than you manually configuring that IPv4 address.

---

# 8. What about all those IPv6 addresses?

You have:

```text
inet6 2401:4900:...
```

and:

```text
inet6 fe80::...
```

These are IPv6 addresses.

For now, **don't get distracted by them**.

You already learned IPv4, and our immediate DevOps networking goal is to become comfortable troubleshooting common Linux systems.

We'll learn the IPv6 basics when they become useful.

---

# 🎯 So what did `ip addr` tell us?

From one command, we learned:

```text
Interface       Status       Address
------------------------------------------------
lo              UP           127.0.0.1
eno1            DOWN         No IPv4 address
wlo1            UP           192.168.1.8/24
```

Therefore, right now your machine is using:

```text
wlo1
  ↓
192.168.1.8/24
```

for its IPv4 network connection.

---

# 🧠 DevOps troubleshooting example

Imagine someone says:

> "This Linux server has no network."

Your first command can be:

```bash
ip addr
```

You might immediately discover:

```text
eth0 ... state DOWN
```

Then you know:

> "The interface itself is down."

Or you might see:

```text
eth0 ... state UP
```

but **no `inet` IPv4 address**.

Then the problem could be:

> "The interface is up, but it hasn't received/configured an IP address."

Or you might see:

```text
eth0 ... inet 10.0.1.25/24
```

Then you know:

> "The interface has an IPv4 address, so we can continue troubleshooting."

That's why `ip addr` is useful.

---

# One important distinction

Don't confuse these:

```text
Interface status
```

with:

```text
Network connectivity
```

For example:

```text
wlo1 = UP
```

does **not** automatically mean:

> "Internet is working."

It only tells us the interface is active.

We still need to check the **route** and then connectivity.

And that's exactly why our next command is:

```bash
ip route
```

## Your next exercise

Before we move on, answer these from **your own output**:

**Q1. Which interface is currently carrying your IPv4 connection?**

**Q2. What is your IPv4 address?**

**Q3. What is your subnet/prefix?**

**Q4. Is `eno1` currently usable as an active wired connection? Why?**

**Q5. Does `wlo1 UP` by itself prove that the Internet is working?**

Answer these five, and then we'll run **`ip route`** and connect your IP address to your default gateway.
