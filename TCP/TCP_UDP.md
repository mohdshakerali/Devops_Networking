# TCP & UDP — Introduction and Basics

## 1. What are TCP and UDP?

TCP and UDP are **Transport Layer protocols** used for transporting data between devices over a network.

They work above the IP layer.

```text
Application
     ↓
 TCP / UDP
     ↓
    IP
     ↓
 Network
```

TCP and UDP solve an important problem:

**IP tells us which device we want to communicate with, but we also need a way to transport application data between that device and the application/service.**

---

# 2. Full Forms

### TCP

**TCP = Transmission Control Protocol**

### UDP

**UDP = User Datagram Protocol**

Both are **Transport Layer protocols**.

---

# 3. IP Address vs Port vs TCP/UDP

We can understand them using a house analogy.

```text
IP Address = House address
Port       = Room number
TCP/UDP    = How the delivery/communication happens
```

### IP Address

An IP address identifies the **destination device/interface**.

Example:

```text
192.168.1.50
```

This tells us:

> "Which device should receive this traffic?"

---

### Port Number

A port identifies a **service/application endpoint** on that device.

Example:

```text
192.168.1.50:22
```

Here:

```text
192.168.1.50 → IP address → Which device?
22           → Port       → Which service?
```

Port `22` is commonly used by **SSH**.

Other common examples:

```text
80   → HTTP
443  → HTTPS
22   → SSH
53   → DNS
```

---

# 4. Why do we need Ports?

Imagine a server has one IP address:

```text
192.168.1.50
```

But the server is running several services:

```text
Web server  → Port 80
HTTPS       → Port 443
SSH         → Port 22
```

If another computer sends traffic to:

```text
192.168.1.50
```

the IP address tells us the destination device.

But the computer still needs to know:

> "Which application/service should receive this traffic?"

That's why we use **port numbers**.

For example:

```text
192.168.1.50:80
```

means:

```text
192.168.1.50 → Destination device
80            → Destination service
```

---

# 5. What is TCP?

**TCP = Transmission Control Protocol**

TCP is a **connection-oriented and reliable Transport Layer protocol**.

TCP is designed to provide reliable delivery of data between applications.

TCP provides mechanisms for:

* Establishing a connection
* Reliable delivery
* Maintaining the correct order of data
* Detecting lost data
* Retransmitting lost data
* Controlling the flow of data

Because TCP provides these mechanisms, it has more overhead than UDP.

### Simple idea:

> **TCP focuses on reliable and ordered delivery.**

---

# 6. What is UDP?

**UDP = User Datagram Protocol**

UDP is a **connectionless Transport Layer protocol**.

UDP provides a simpler way to send data without TCP's connection establishment and built-in reliability mechanisms.

UDP does not provide built-in guarantees that:

* Data will arrive
* Data will arrive only once
* Data will arrive in the same order it was sent

UDP has less overhead than TCP.

### Simple idea:

> **UDP focuses on simplicity and low overhead.**

---

# 7. TCP vs UDP — Main Difference

| TCP                           | UDP                                   |
| ----------------------------- | ------------------------------------- |
| Transmission Control Protocol | User Datagram Protocol                |
| Connection-oriented           | Connectionless                        |
| Reliable delivery             | No built-in delivery guarantee        |
| Maintains ordering            | No built-in ordering guarantee        |
| Can retransmit lost data      | Does not retransmit lost data itself  |
| More overhead                 | Less overhead                         |
| More transport mechanisms     | Simpler transport mechanism           |
| Reliability is important      | Low overhead/latency may be important |

---

# 8. The Most Important Difference

Remember this first:

```text
TCP
↓
Reliability + Ordering
```

```text
UDP
↓
Simplicity + Low overhead
```

A common simplified way of remembering it is:

> **TCP = Reliability**

> **UDP = Speed + Simplicity**

However, don't think:

```text
TCP = always slow
UDP = always fast
```

That's not accurate.

The better understanding is:

> TCP provides reliability mechanisms that add overhead.

> UDP provides a minimal transport mechanism and leaves reliability/application behavior largely to the application.

---

# 9. Example — Downloading a File

Suppose we are downloading a 1 GB file.

We want the complete and correct file.

Imagine the original data is:

```text
A B C D E F G H
```

During transmission:

```text
A B C   E F G H
      ↑
      D was lost
```

With TCP, the loss can be detected and the missing data can be retransmitted.

Eventually the application can receive:

```text
A B C D E F G H
```

This is why TCP is useful when **reliable and complete delivery is important**.

---

# 10. Why UDP Can Be Useful

Imagine a real-time communication application.

Data is continuously being sent:

```text
A B C D E F G H I J
```

If one piece is lost, the application may prefer to continue receiving newer data instead of waiting for the old piece.

For some real-time applications:

```text
Low delay
    ↓
More important
than
Perfect delivery of every packet
```

UDP's simpler design can be useful in such situations.

---

# 11. Important Mental Model

Think about the networking stack like this:

```text
Application
     ↓
   TCP/UDP
     ↓
    IP
     ↓
 Network
```

Each part has a different job.

### IP

> Which device?

### Port

> Which application/service?

### TCP/UDP

> How should the application data be transported?

---

# 12. Example

Suppose we have:

```text
192.168.1.50:443
```

We can understand it as:

```text
192.168.1.50
      ↓
Destination IP address
      ↓
Which device?
```

and:

```text
443
 ↓
Destination port
 ↓
Which service?
```

If the communication uses TCP:

```text
TCP
 ↓
Reliable, ordered transport
```

So conceptually:

```text
Application
     ↓
   TCP
     ↓
  Port 443
     ↓
IP: 192.168.1.50
     ↓
  Network
```

---

# 13. What We Have Learned So Far

We can summarize everything as:

```text
IPv4
 ↓
Identifies devices using IP addresses

Port
 ↓
Identifies application/service endpoints

TCP
 ↓
Transmission Control Protocol
 ↓
Connection-oriented
 ↓
Reliable
 ↓
Ordered
 ↓
Retransmission of lost data
 ↓
More overhead

UDP
 ↓
User Datagram Protocol
 ↓
Connectionless
 ↓
No built-in reliability
 ↓
No built-in ordering
 ↓
Less overhead
 ↓
Simpler transport
```

## Golden Rules to Remember

### Rule 1

> **IP address identifies the destination device/interface.**

### Rule 2

> **Port identifies the application/service endpoint.**

### Rule 3

> **TCP and UDP are Transport Layer protocols.**

### Rule 4

> **TCP provides reliable, ordered transport.**

### Rule 5

> **UDP provides a simpler, connectionless transport with less overhead and no built-in reliability.**

### Rule 6

> **TCP is not simply "slow" and UDP is not simply "fast."**

The important difference is the **features and guarantees they provide**.
