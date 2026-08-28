# TCP — 3-Way Handshake, Sequence Numbers & Acknowledgments

## 1. TCP 3-Way Handshake

TCP is **connection-oriented**.

Before normal data transfer begins, TCP establishes a connection between the client and server.

The process is called the:

> **TCP 3-Way Handshake**

It consists of:

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

Conceptually, it means:

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

This contains:

* SYN flag
* ACK flag

Conceptually, the server is saying:

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

The client receives the server's SYN-ACK and sends an:

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

# 5. Simple Phone-Call Analogy

The TCP handshake can be understood using a phone-call analogy.

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

Now the connection is established.

### Important:

This is only an analogy to understand the concept.

The actual TCP handshake uses TCP flags, sequence numbers, and acknowledgment numbers.

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

TCP needs to keep track of the position of data in the communication stream.

TCP works with a **stream of bytes**.

For example:

```text
H E L L O
```

Conceptually, each byte can have a sequence position:

```text
H → 100
E → 101
L → 102
L → 103
O → 104
```

These numbers are only examples to understand the concept.

The actual TCP sequence numbers are different and will be learned later.

---

# 8. Why Does TCP Need Sequence Numbers?

One important responsibility of TCP is to provide **ordered delivery**.

Imagine the sender sends:

```text
A B C D E
```

But the receiver receives:

```text
A B D C E
```

The data arrived out of order.

Sequence numbers help TCP identify the position of each piece of data.

For example:

```text
A → 100
B → 101
C → 102
D → 103
E → 104
```

The receiver can understand:

```text
100 → A
101 → B
102 → C
103 → D
104 → E
```

Therefore, sequence numbers help TCP:

* Track data position
* Detect ordering
* Identify missing data
* Reconstruct the data stream correctly

---

# 9. Example of Missing Data

Suppose the sender sends:

```text
A B C D E
```

But the receiver gets:

```text
A B C E
```

If the sequence numbers were:

```text
A → 100
B → 101
C → 102
D → 103
E → 104
```

The receiver can see:

```text
100 101 102 104
 A   B   C   E
```

It can recognize:

> Sequence number 103 is missing.

Therefore:

> **D is missing.**

---

# 10. Acknowledgment (ACK)

**ACK = Acknowledgment**

TCP uses acknowledgments to communicate what data has been successfully received.

The receiver can tell the sender, conceptually:

> "I have received everything up to this point, and I expect the next byte."

For example:

```text
Sender                         Receiver

A B C ───────────────────────→

                              "I received A, B and C.
                               I expect the next data."
                                      │
         ←──────── ACK ───────────────┘
```

The ACK is sent by the **receiver** and received by the **sender**.

---

# 11. ACK Helps TCP Provide Reliability

Suppose:

```text
Sender                         Receiver

A B C D E ───────────────────→

                              A B C E
```

The receiver notices that D is missing.

It can send an acknowledgment indicating, conceptually:

> "I have received everything up to C. I expect D next."

```text
Sender                         Receiver

                              A B C E
                                  │
         ←────── ACK ────────────┘
```

The sender can then retransmit the missing data:

```text
Sender                         Receiver

        D ───────────────────→
```

Now the receiver can obtain the missing data.

---

# 12. Sequence Number vs Acknowledgment Number

These two concepts are different.

### Sequence Number

A sequence number identifies the position of data in the TCP byte stream.

```text
Sequence Number
       ↓
"Where does this data belong?"
```

### Acknowledgment Number

An acknowledgment number tells the sender what data the receiver expects next.

```text
Acknowledgment Number
       ↓
"What byte do I expect next?"
```

---

# 13. Don't Confuse TCP Flags with Numbers

### SYN

```text
SYN
 ↓
TCP flag
```

It is used during connection establishment.

### ACK

```text
ACK
 ↓
TCP flag
```

It indicates an acknowledgment.

### Sequence Number

```text
Sequence Number
 ↓
Number used to track position in the byte stream
```

### Acknowledgment Number

```text
Acknowledgment Number
 ↓
Number indicating the next byte expected
```

So:

> **SYN is not a sequence number.**

> **ACK flag is not the same thing as acknowledgment number.**

This distinction is very important.

---

# 14. Retransmission

If TCP detects that data has not been successfully received, TCP can retransmit the missing data.

Conceptually:

```text
Sender                         Receiver

A B C D E ───────────────────→

                              A B C E
                              D missing
                                  │
         ←──── ACK ──────────────┘
                                  │

        D ─────────────────────→
```

This is one of the mechanisms that allows TCP to provide **reliable delivery**.

---

# 15. Complete Picture

TCP's behavior can now be understood like this:

```text
             TCP
              │
              ↓
      Establish connection
              │
              ↓
     SYN → SYN-ACK → ACK
              │
              ↓
      Connection established
              │
              ↓
         Data transfer
              │
              ↓
      Sequence Numbers
              │
              ↓
      Track data position
              │
              ↓
       Acknowledgments
              │
              ↓
     Confirm received data
              │
              ↓
        Missing data?
          /       \
        Yes        No
         ↓          ↓
   Retransmit     Continue
```

---

# 16. Key Points to Remember

### TCP 3-Way Handshake

> **SYN → SYN-ACK → ACK**

### SYN

> Request to establish a TCP connection.

### SYN-ACK

> Server acknowledges the client's request and responds with its own SYN.

### ACK

> Acknowledgment of the server's response.

### Sequence Number

> Tracks the position of data in the TCP byte stream.

### Acknowledgment Number

> Indicates the next byte the receiver expects.

### Retransmission

> TCP can resend missing data to provide reliable delivery.

---

# 17. Current TCP Learning Map

```text
TCP
│
├── Connection-oriented
│
├── 3-Way Handshake
│      ├── SYN
│      ├── SYN-ACK
│      └── ACK
│
├── Sequence Numbers
│      └── Track data position/order
│
├── Acknowledgments
│      └── Tell sender what has been received
│
└── Retransmission
       └── Resend missing data
```

### Golden Rule

> **TCP establishes a connection, numbers the data stream, acknowledges received data, and can retransmit missing data to provide reliable and ordered delivery.**
