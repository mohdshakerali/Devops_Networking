# TCP — Duplicate ACKs, Fast Retransmit & Retransmission Timeout

## 1. Duplicate ACK

A **Duplicate ACK** occurs when the receiver sends the **same acknowledgment number again** because it is still waiting for missing data.

Example:

```text
Segment 1 → 1000–1004 ✅
Segment 2 → 1005–1009 ❌ LOST
Segment 3 → 1010–1014 ✅
Segment 4 → 1015–1019 ✅
```

The receiver has:

```text
1000–1004 ✅
1005–1009 ❌
1010–1014 ✅
1015–1019 ✅
```

The receiver is still waiting for:

```text
1005
```

Therefore, it can send:

```text
ACK 1005
ACK 1005
ACK 1005
```

The repeated `ACK 1005` messages are **Duplicate ACKs**.

### Important:

> **The ACK number stays the same because the next expected byte has not changed.**

---

# 2. Why Does TCP Send Duplicate ACKs?

Suppose the receiver has:

```text
1000–1004 ✅
```

and then receives:

```text
1010–1014 ✅
```

The receiver notices a gap:

```text
1000–1004 ✅
1005–1009 ❌
1010–1014 ✅
```

It cannot move its cumulative ACK beyond `1005` because it is still missing the data beginning at `1005`.

Therefore:

```text
ACK = 1005
```

If more later data arrives, the receiver can continue sending:

```text
ACK 1005
```

These repeated ACKs provide a signal to the sender that something may be missing.

---

# 3. Fast Retransmit

**Fast Retransmit** is a TCP mechanism that allows the sender to retransmit suspected missing data **without waiting for the retransmission timer to expire**.

The classic rule is:

> **3 duplicate ACKs → Fast Retransmit**

Example:

```text
Segment 1 → 1000–1004 ✅
Segment 2 → 1005–1009 ❌ LOST
Segment 3 → 1010–1014 ✅
Segment 4 → 1015–1019 ✅
Segment 5 → 1020–1024 ✅
```

The receiver repeatedly sends:

```text
ACK 1005
ACK 1005
ACK 1005
```

The sender receives **3 duplicate ACKs**.

It thinks:

> "There is strong evidence that something is missing around sequence number 1005."

TCP can then quickly retransmit:

```text
1005–1009
```

without waiting for the retransmission timeout.

---

# 4. Important Clarification About Fast Retransmit

Three duplicate ACKs do **not** prove with 100% certainty that a segment was lost.

They are a strong indication that:

> **Data is missing or has arrived out of order.**

TCP can then retransmit the suspected missing data.

---

# 5. Retransmission Timeout (RTO)

**RTO = Retransmission Timeout**

Sometimes the sender does not receive the expected acknowledgment.

For example:

```text
Segment 1 → 1000–1004 ✅
Segment 2 → 1005–1009 ❌ LOST
```

Suppose no later data arrives.

The sender waits for the acknowledgment:

```text
Waiting...
   ↓
Waiting...
   ↓
Waiting...
   ↓
Timer expires
```

The retransmission timer expires.

Then TCP retransmits the unacknowledged data:

```text
1005–1009
```

This is a **Retransmission Timeout**.

---

# 6. Fast Retransmit vs Retransmission Timeout

|                 | Fast Retransmit                        | Retransmission Timeout                       |
| --------------- | -------------------------------------- | -------------------------------------------- |
| Main signal     | Duplicate ACKs                         | Expected ACK doesn't arrive in time          |
| Classic trigger | 3 duplicate ACKs                       | Retransmission timer expires                 |
| Wait for timer? | ❌ No                                   | ✅ Yes                                        |
| Reaction        | Fast                                   | Slower                                       |
| Purpose         | Recover suspected missing data quickly | Recover data when ACK hasn't arrived in time |

---

# 7. Fast Retransmit Example

```text
Sender                         Receiver

1000–1004 ───────────────────→
                              ACK 1005
                    ←─────────

1005–1009 ───────X             LOST

1010–1014 ───────────────────→
                              ACK 1005
                    ←─────────

1015–1019 ───────────────────→
                              ACK 1005
                    ←─────────

1020–1024 ───────────────────→
                              ACK 1005
                    ←─────────

             3 Duplicate ACKs
                    ↓
             Fast Retransmit
                    ↓
1005–1009 ───────────────────→
```

---

# 8. Retransmission Timeout Example

```text
Sender                         Receiver

1000–1004 ───────────────────→
                              ACK 1005
                    ←─────────

1005–1009 ───────X             LOST

        No later data arrives
                 ↓
              Waiting
                 ↓
          ⏱️ Timer expires
                 ↓
1005–1009 ───────────────────→
```

---

# 9. Simple Analogy

## Fast Retransmit

Imagine you send someone a series of documents.

You send:

```text
Document 1
Document 2
Document 3
Document 4
```

The person repeatedly says:

> "I haven't received Document 2."

You hear this several times and think:

> "Something is wrong with Document 2."

So you send Document 2 again immediately.

This is similar to:

> **Fast Retransmit**

---

## Retransmission Timeout

You send Document 2.

Then you hear nothing.

You wait...

Still nothing.

Eventually you decide:

> "I've waited long enough. I'll send it again."

This is similar to:

> **Retransmission Timeout**

---

# 10. The Big Picture

```text
                    TCP DATA
                       │
                       ↓
                  ACK expected
                       │
              ┌────────┴────────┐
              ↓                 ↓
       Duplicate ACKs      No ACK arrives
              │                 │
              ↓                 ↓
      3 Duplicate ACKs    Timer continues
              │                 │
              ↓                 ↓
      Fast Retransmit       Timer expires
              │                 │
              ↓                 ↓
       Retransmit data      Retransmit data
```

---

# 11. How This Fits With Everything We Learned

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
│      └── Track byte position
│
├── Acknowledgment Numbers
│      └── Tell sender the next byte expected
│
├── Missing Data
│      └── Sequence numbers help identify gaps
│
├── Duplicate ACKs
│      └── Same ACK sent because missing data is still expected
│
├── Fast Retransmit
│      └── Classic trigger: 3 duplicate ACKs
│
└── Retransmission Timeout
       └── Timer expires when expected ACK doesn't arrive
```

---

# 12. Golden Rules

> **Duplicate ACK = Same ACK number is received/sent repeatedly because the receiver is still waiting for earlier data.**

> **3 duplicate ACKs = classic Fast Retransmit trigger.**

> **Fast Retransmit = retransmit suspected missing data without waiting for timeout.**

> **Retransmission Timeout = retransmit after the retransmission timer expires.**

> **Fast Retransmit is generally faster than waiting for a timeout.**

> **Neither mechanism means TCP knows with absolute certainty that the data was lost; delay or reordering can also contribute to duplicate ACKs.**
