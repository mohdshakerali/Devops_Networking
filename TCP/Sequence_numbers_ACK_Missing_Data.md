# TCP — 3-Way Handshake, Sequence Numbers, ACK & Missing Data

## 1. TCP 3-Way Handshake

TCP is **connection-oriented**.

Before normal data transfer begins, TCP establishes a connection between the client and server.

This process is called the:

> **TCP 3-Way Handshake**

The three steps are:

```text
SYN
 ↓
SYN-ACK
 ↓
ACK
```

After these three steps, the TCP connection is established.

---

# 2. Step 1 — SYN

**SYN = Synchronize**

The client sends a SYN to the server.

Conceptually:

> "I want to establish a TCP connection with you."

```text
Client                         Server

  │
  │ -------- SYN ------------>│
  │                            │
```

SYN is a **TCP flag**.

For now, remember:

> **SYN = Request to establish a TCP connection.**

---

# 3. Step 2 — SYN-ACK

The server receives the SYN and responds with:

> **SYN-ACK**

It contains:

* SYN flag
* ACK flag

Conceptually:

> "I received your request, and I'm ready to establish the connection too."

```text
Client                         Server

  │
  │ -------- SYN ------------>│
  │                            │
  │<------- SYN-ACK -----------│
  │                            │
```

---

# 4. Step 3 — ACK

The client receives the server's SYN-ACK and sends:

> **ACK = Acknowledgment**

Conceptually:

> "I received your response."

```text
Client                         Server

  │
  │ -------- SYN ------------>│
  │                            │
  │<------- SYN-ACK -----------│
  │                            │
  │ -------- ACK ------------>│
  │                            │
```

Now:

> **TCP connection is established.**

Normal application data can now be transferred.

---

# 5. Phone Call Analogy

The TCP handshake can be understood using a phone-call analogy:

```text
Client                         Server

"Hi, can you hear me?"
       SYN
  ───────────────────────────→

                         "Yes, I can hear you.
                          Can you hear me?"
                              SYN-ACK
  ←───────────────────────────

"Yes, I can hear you too."
       ACK
  ───────────────────────────→
```

Now both sides have established the necessary connection state.

This is only an analogy. The real TCP handshake uses TCP flags and sequence/acknowledgment numbers.

---

# 6. Why Three Messages?

The three messages allow both sides to establish the necessary communication state.

```text
Client:
"I want to connect." ✅

Server:
"I received your request." ✅
"I'm ready too." ✅

Client:
"I received your response." ✅
```

Therefore:

```text
SYN
 ↓
SYN-ACK
 ↓
ACK
 ↓
Connection established
```

---

# 7. Sequence Numbers

TCP works with a **stream of bytes**.

TCP uses sequence numbers to identify the position of data in that byte stream.

For example, suppose:

```text
H E L L O
```

Conceptually:

```text
H → 100
E → 101
L → 102
L → 103
O → 104
```

These numbers are only examples for understanding.

The actual TCP sequence numbers are different and will be learned separately.

---

# 8. Why Does TCP Need Sequence Numbers?

TCP needs to provide **ordered delivery**.

Suppose the sender sends:

```text
A B C D E
```

but the receiver receives:

```text
A B D C E
```

The data arrived out of order.

Sequence numbers help TCP identify where each byte belongs.

For example:

```text
A → 100
B → 101
C → 102
D → 103
E → 104
```

The receiver can identify:

```text
100 → A
101 → B
102 → C
103 → D
104 → E
```

Therefore, sequence numbers help TCP:

* Track data position
* Maintain order
* Identify missing data
* Reconstruct the byte stream correctly

---

# 9. Sequence Number vs ACK

These are different concepts.

### Sequence Number

The sequence number tracks the position of data in the TCP byte stream.

Think:

> **"Where does this data belong?"**

### Acknowledgment Number

The acknowledgment number tells the sender what byte the receiver expects next.

Think:

> **"What byte do I expect next?"**

---

# 10. ACK — Acknowledgment

**ACK = Acknowledgment**

TCP uses acknowledgments to communicate what data has been received.

The receiver can tell the sender, conceptually:

> "I have received everything up to this point, and I expect the next byte."

For example:

```text
Sender                         Receiver

A B C ───────────────────────→

                              "I received A, B and C.
                               I expect the next byte."

       ←──── ACK ─────────────
```

The receiver sends the ACK.

The sender receives the ACK.

---

# 11. Golden Rule of ACK

The most important rule:

> **ACK number = next byte expected**

It is NOT:

> ❌ The last byte received.

It IS:

> ✅ The next byte expected.

---

# 12. Simple ACK Example

Suppose:

```text
A → 100
B → 101
C → 102
D → 103
```

The receiver receives all four bytes.

The last byte received is:

```text
103
```

The next byte expected is:

```text
104
```

Therefore:

```text
ACK = 104
```

Remember:

```text
Last byte received = 103
Next byte expected = 104
ACK = 104
```

---

# 13. Missing Byte Example

Suppose:

```text
A → 200
B → 201
C → 202
D → 203
E → 204
```

But the receiver receives:

```text
A → 200
B → 201
C → 202
E → 204
```

D is missing:

```text
D → 203
```

The receiver has received everything through:

```text
202
```

The next byte expected is:

```text
203
```

Therefore:

```text
ACK = 203
```

The receiver is essentially saying:

> "I have received everything up to 202. I expect 203 next."

---

# 14. Important Detail About Missing Data

Even if a later byte arrives, the ACK still represents the **next byte expected in the ordered stream**.

For example:

```text
Received:

200 201 202    204
 A   B   C      E

Missing:

203
 D
```

The receiver is still waiting for:

```text
203
```

Therefore:

```text
ACK = 203
```

It does not simply ACK 205 because byte 203 is still missing.

---

# 15. Sequence Numbers Increase With Data

The sequence number advances according to the **number of bytes sent**.

Suppose:

```text
Starting Sequence Number = 1000
Data = 5 bytes
```

Then:

```text
1000 → Byte 1
1001 → Byte 2
1002 → Byte 3
1003 → Byte 4
1004 → Byte 5
```

Therefore:

```text
Sequence range = 1000–1004
Next byte = 1005
ACK = 1005
```

---

# 16. Sequence Number Does NOT Mean Amount of Data

If the starting sequence number is:

```text
1000
```

that does NOT mean:

> ❌ "1000 represents 1 byte."

Instead:

> **1000 is simply the sequence number assigned to the first byte.**

Think of sequence numbers like seat numbers:

```text
Seat 1000 → Byte 1
Seat 1001 → Byte 2
Seat 1002 → Byte 3
...
Seat 1099 → Byte 100
```

Therefore:

```text
1000–1099 = 100 bytes
```

---

# 17. Formula

If:

```text
Starting Sequence Number = X
Number of Bytes = N
```

Then:

```text
First byte = X

Last byte = X + N - 1

ACK = X + N
```

Example:

```text
Starting Seq = 2000
Data = 10 bytes
```

Therefore:

```text
First byte = 2000
Last byte = 2009
ACK = 2010
```

---

# 18. Multiple TCP Segments

TCP sends data in multiple **segments**.

Suppose the sender has:

```text
A B C D E F G H I J
```

TCP can divide it into two segments.

### Segment 1

```text
Sequence Number = 100
Data = A B C D E
```

Sequence numbers:

```text
100 101 102 103 104
 A   B   C   D   E
```

Next sequence number:

```text
105
```

### Segment 2

```text
Sequence Number = 105
Data = F G H I J
```

Sequence numbers:

```text
105 106 107 108 109
 F   G   H   I   J
```

If everything arrives:

```text
100–109
```

The next byte expected is:

```text
110
```

Therefore:

```text
ACK = 110
```

---

# 19. Missing Segment Example

Suppose there are three segments:

### Segment 1

```text
Sequence Number = 1000
Data = 5 bytes

1000–1004
```

### Segment 2

```text
Sequence Number = 1005
Data = 5 bytes

1005–1009
```

### Segment 3

```text
Sequence Number = 1010
Data = 5 bytes

1010–1014
```

Now:

```text
Segment 1 → Arrives ✅
Segment 2 → LOST ❌
Segment 3 → Arrives ✅
```

The receiver has:

```text
1000–1004 ✅
1005–1009 ❌
1010–1014 ✅
```

The receiver is still waiting for:

```text
1005
```

Therefore:

```text
ACK = 1005
```

The missing data is:

```text
1005–1009
```

Notice:

```text
Missing DATA = 1005–1009

ACK = 1005
```

The ACK is a **single number**, not a range.

---

# 20. Another Missing Segment Example

Suppose:

```text
Segment 1 → 2000–2004
Segment 2 → 2005–2009
Segment 3 → 2010–2014
```

But:

```text
Segment 1 → Arrives ✅
Segment 2 → LOST ❌
Segment 3 → Arrives ✅
```

The receiver has:

```text
2000–2004 ✅
2005–2009 ❌
2010–2014 ✅
```

The first missing/next expected byte is:

```text
2005
```

Therefore:

```text
ACK = 2005
```

The missing data is:

```text
2005–2009
```

---

# 21. Sequence Number vs ACK — Final Understanding

Think of it this way:

### Sequence Number

```text
"Here is where my data starts."
```

### Data Length

```text
"Here is how many bytes I am sending."
```

### ACK Number

```text
"Here is the next byte I expect."
```

Example:

```text
Sequence Number = 1000
Data Length = 5 bytes

1000–1004
   ↓
Next expected = 1005
   ↓
ACK = 1005
```

---

# 22. TCP Reliability — Current Understanding

We can now connect everything:

```text
TCP
│
├── Connection-oriented
│
├── 3-Way Handshake
│      │
│      ├── SYN
│      ├── SYN-ACK
│      └── ACK
│
├── Sequence Numbers
│      ↓
│   Track byte position/order
│
├── Acknowledgments
│      ↓
│   Tell sender the next byte expected
│
├── Detect gaps/missing data
│
└── Retransmission
       ↓
   Missing data can be sent again
```

---

# 23. Golden Rules

### Rule 1

> **SYN → SYN-ACK → ACK = TCP 3-Way Handshake**

### Rule 2

> **SYN is a TCP flag.**

### Rule 3

> **ACK is a TCP flag.**

### Rule 4

> **Sequence Number tracks the position of data in the byte stream.**

### Rule 5

> **ACK Number = next byte expected.**

### Rule 6

> **Sequence numbers increase according to the number of bytes sent.**

### Rule 7

> **A data segment occupies a range of sequence numbers.**

### Rule 8

> **An ACK is a single number, not a range.**

### Rule 9

> **TCP can use acknowledgments, sequence numbers, and retransmission mechanisms to provide reliable, ordered delivery.**

---

# 24. Current Learning Map

So far we have learned:

```text
TCP
│
├── Transmission Control Protocol
│
├── Transport Layer
│
├── Connection-oriented
│
├── 3-Way Handshake
│     ├── SYN
│     ├── SYN-ACK
│     └── ACK
│
├── Sequence Numbers
│
├── Acknowledgment Numbers
│
├── Ordered data
│
├── Missing data
│
└── Retransmission concept
```

## Next Topic

The next step is to understand **how TCP detects missing data and triggers retransmission**, including:

```text
Duplicate ACKs
      ↓
Fast Retransmit
      ↓
Retransmission Timeout
```

We will learn these slowly with examples rather than memorizing them.
