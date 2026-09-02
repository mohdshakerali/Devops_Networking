Yes. **Google.com is actually a very good real-world example** for learning SOA because we can look at an actual SOA record rather than a made-up one.

Let's use:

```bash
dig google.com SOA
```

A current DNS lookup shows Google's SOA information along these lines:

```text
google.com.  60  IN  SOA  ns1.google.com.  dns-admin.google.com.
                           973049826
                           900
                           900
                           1800
                           60
```

The exact serial number/TTL can change over time, but the structure is what we're learning. ([Google Public DNS][1])

Now let's understand **what each part means**.

### `google.com.`

This is the **DNS zone** we're asking about.

We are basically asking:

> "Give me the SOA information for the `google.com` zone."

---

### `IN`

**IN** stands for **Internet**.

You've already seen this in your `dig` output:

```text
IN A
IN AAAA
IN MX
IN SOA
```

For normal Internet DNS records, you'll commonly see `IN`.

---

### `SOA`

**SOA = Start of Authority**

This tells us:

> "Here is the authoritative information about this DNS zone."

Google's DNS infrastructure uses `ns1.google.com` as the SOA **MNAME**, while Google also has multiple authoritative name servers (`ns1` through `ns4`). ([Google for Developers][2])

---

### `ns1.google.com.`

This is the first important field.

It is called **MNAME** — **Master Name**.

```text
google.com
     ↓
SOA
     ↓
ns1.google.com
```

So the SOA is telling us that `ns1.google.com` is the **primary/master server named by the SOA** for the zone.

Don't confuse this with saying:

> "Google has only one DNS server."

That's not true.

Google has multiple authoritative DNS servers. The **NS records** show those authoritative servers.

You can see them with:

```bash
dig google.com NS
```

You'll find Google's authoritative nameservers such as:

```text
ns1.google.com
ns2.google.com
ns3.google.com
ns4.google.com
```

---

### `dns-admin.google.com.`

This is the **RNAME** field.

It represents the responsible administrator's email address in DNS format.

The DNS notation uses a dot where you would normally have `@`.

So:

```text
dns-admin.google.com.
```

represents approximately:

```text
dns-admin@google.com
```

This is a little unusual at first, but you'll recognize it when you see SOA records.

---

### `973049826`

This is the **SERIAL**.

Think of it as the **version number of the DNS zone**.

```text
SERIAL = 973049826
```

If Google's DNS zone is updated, the serial can be increased.

Why does that matter?

Because secondary DNS servers can compare their zone version against the primary's version.

For example:

```text
Primary:
SERIAL = 973049826

Secondary:
SERIAL = 973049825
```

The secondary can recognize:

> "My DNS zone information is older."

That's one of the major reasons the SOA serial exists.

---

### `900`

This is **REFRESH**.

```text
900 seconds = 15 minutes
```

It tells a secondary DNS server how frequently it should check whether the zone has been updated.

---

### another `900`

This is **RETRY**.

```text
900 seconds = 15 minutes
```

If the secondary tries to refresh the zone but the primary cannot be contacted, this tells it when to try again.

---

### `1800`

This is **EXPIRE**.

```text
1800 seconds = 30 minutes
```

This tells a secondary how long it can continue using its existing zone data if it cannot successfully refresh from the primary.

---

### `60`

This is the **MINIMUM** field.

For modern DNS, this field is primarily relevant to **negative caching**.

Suppose you ask:

```bash
dig doesnotexist.google.com
```

and DNS determines that the name doesn't exist.

That negative result can be cached according to the SOA's negative-caching TTL.

Here:

```text
60 seconds
```

So the resolver can cache that negative result for the applicable period.

Google's current SOA values are also documented by current DNS lookup data as `900` refresh, `900` retry, `1800` expire, and `60` minimum. ([Whois][3])

---

So when you see:

```text
google.com. IN SOA ns1.google.com. dns-admin.google.com. 973049826 900 900 1800 60
```

you should mentally read it as:

```text
google.com
   ↓
SOA information

Primary/Master:       ns1.google.com
Responsible contact:  dns-admin@google.com
Zone version:         973049826
Refresh:              15 minutes
Retry:                15 minutes
Expire:               30 minutes
Negative caching:     60 seconds
```

### And this is the important distinction

Earlier you learned **NS**:

```text
NS → "Which DNS servers are authoritative for google.com?"
```

And now **SOA**:

```text
SOA → "What are the authoritative/administrative details of the google.com DNS zone?"
```

So these two records are related, but they answer **different questions**.

And since you're using Zorin, you can run this yourself:

```bash
dig google.com SOA
```

Then paste the **entire output here**. We'll read **your actual output line by line**, including the `HEADER`, `QUESTION`, `ANSWER`, `SERVER`, and `Query time` sections, instead of using a theoretical output.

[1]: https://dns.google/query?name=google.com&type=SOA&utm_source=chatgpt.com "Query: google.com - Google Public DNS"
[2]: https://developers.google.com/speed/public-dns/docs/intro?utm_source=chatgpt.com "Introduction to Google Public DNS  |  Google for Developers"
[3]: https://who.is/dns/google.com?utm_source=chatgpt.com "google.com DNS Information - Who.is"

Example


; <<>> DiG 9.18.39-0ubuntu0.24.04.7-Ubuntu <<>> google.com SOA
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 25519
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 4, ADDITIONAL: 9

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;google.com.			IN	SOA

;; ANSWER SECTION:
google.com.		56	IN	SOA	ns1.google.com. dns-admin.google.com. 974394558 900 900 1800 60

;; AUTHORITY SECTION:
google.com.		327967	IN	NS	ns4.google.com.
google.com.		327967	IN	NS	ns1.google.com.
google.com.		327967	IN	NS	ns2.google.com.
google.com.		327967	IN	NS	ns3.google.com.

;; ADDITIONAL SECTION:
ns2.google.com.		155584	IN	A	216.239.34.10
ns2.google.com.		155584	IN	AAAA	2001:4860:4802:34::a
ns1.google.com.		155584	IN	A	216.239.32.10
ns1.google.com.		155584	IN	AAAA	2001:4860:4802:32::a
ns3.google.com.		216719	IN	A	216.239.36.10
ns3.google.com.		127914	IN	AAAA	2001:4860:4802:36::a
ns4.google.com.		262538	IN	A	216.239.38.10
ns4.google.com.		19484	IN	AAAA	2001:4860:4802:38::a

;; Query time: 73 msec
;; SERVER: 127.0.0.53#53(127.0.0.53) (UDP)
;; WHEN: Wed Sep 02 05:22:20 IST 2026
;; MSG SIZE  rcvd: 333

Perfect. This is **your actual `dig` output**, so let's learn SOA from exactly what your machine received.

The most important line for us is:

```text
google.com.  56  IN  SOA  ns1.google.com. dns-admin.google.com. 974394558 900 900 1800 60
```

Don't worry about the other sections yet. We'll connect them as we go.

### First: what did you ask DNS?

You ran:

```bash
dig google.com SOA
```

That means:

> **"Give me the SOA record for google.com."**

And your DNS resolver returned **one SOA record**:

```text
google.com. IN SOA ...
```

The `IN` means **Internet**, and `SOA` means **Start of Authority**.

Now let's read the SOA fields from left to right.

---

### `google.com.`

This is the **zone** for which we're getting SOA information.

```text
google.com.
```

So we're asking about the DNS zone for `google.com`.

---

### `ns1.google.com.`

Your output says:

```text
SOA ns1.google.com.
```

This is the **MNAME — Master Name** field.

It identifies the primary/master authoritative DNS server named by the SOA.

So:

```text
ns1.google.com.
```

is the server specified as the master for this zone.

Notice something interesting in your output.

The **AUTHORITY SECTION** contains:

```text
google.com.  IN  NS  ns4.google.com.
google.com.  IN  NS  ns1.google.com.
google.com.  IN  NS  ns2.google.com.
google.com.  IN  NS  ns3.google.com.
```

So Google has **four authoritative nameservers** listed:

```text
ns1
ns2
ns3
ns4
```

But the SOA specifically identifies:

```text
MNAME = ns1.google.com
```

That's why **NS and SOA are related but not the same thing**.

---

### `dns-admin.google.com.`

Next:

```text
dns-admin.google.com.
```

This is the **RNAME — Responsible Name** field.

It represents a responsible administrator's email address using DNS notation.

So:

```text
dns-admin.google.com.
```

corresponds roughly to:

```text
dns-admin@google.com
```

The `.` replaces the `@` in the DNS representation.

---

### `974394558`

Now we reach a very important field:

```text
974394558
```

This is the **SERIAL**.

Think of this as the **version number of the DNS zone**.

For example, imagine a secondary DNS server has:

```text
SERIAL = 974394557
```

while the master has:

```text
SERIAL = 974394558
```

The secondary can determine:

> "The master has a newer version of the zone."

This becomes important when DNS data is replicated between authoritative DNS servers.

For you as a DevOps engineer, remember:

**SOA serial = DNS zone version.**

---

### `900`

Next:

```text
900
```

This is **REFRESH**.

```text
900 seconds = 15 minutes
```

It tells a secondary DNS server approximately how often it should check the master for changes.

---

### Second `900`

Then:

```text
900
```

This is **RETRY**.

Again:

```text
900 seconds = 15 minutes
```

If the secondary tries to contact the master but the attempt fails, this is the retry interval.

So:

```text
REFRESH = 900
RETRY   = 900
```

---

### `1800`

Next:

```text
1800
```

This is **EXPIRE**.

```text
1800 seconds = 30 minutes
```

It tells a secondary how long it can continue serving its existing zone data if it cannot successfully refresh from the master.

You don't need to memorize the operational details yet. Just recognize:

```text
1800 = EXPIRE
```

---

### `60`

Finally:

```text
60
```

This is the **MINIMUM** field.

For modern DNS, this is primarily used in **negative caching**.

For example, suppose someone asks DNS:

```text
doesnotexist.google.com
```

and the authoritative DNS server says:

```text
NXDOMAIN
```

**NXDOMAIN = Non-Existent Domain**

A resolver can cache that negative answer according to the applicable negative-caching TTL.

Your SOA has:

```text
MINIMUM = 60
```

So this value is relevant to that negative caching behavior.

---

## Now read your entire SOA

Your actual line:

```text
google.com. IN SOA ns1.google.com. dns-admin.google.com. 974394558 900 900 1800 60
```

can now be read as:

```text
Zone:
google.com

MNAME / Master:
ns1.google.com

RNAME / Responsible contact:
dns-admin@google.com

SERIAL:
974394558

REFRESH:
900 seconds = 15 minutes

RETRY:
900 seconds = 15 minutes

EXPIRE:
1800 seconds = 30 minutes

MINIMUM:
60 seconds
```

### And there's one more thing in your output that's important

You have:

```text
;; SERVER: 127.0.0.53#53(127.0.0.53) (UDP)
```

This is **not Google's DNS server**.

This is your **local DNS resolver/stub** on your Zorin machine.

Your machine sent the DNS query to:

```text
127.0.0.53
port 53
UDP
```

Your local resolver then handled the DNS resolution and returned Google's SOA information to `dig`.

This connects directly to what we learned earlier about your Zorin system and `systemd-resolved`.

So your actual journey here was roughly:

```text
dig
 ↓
127.0.0.53:53
 ↓
DNS resolution
 ↓
Google's authoritative DNS information
 ↓
SOA record returned
 ↓
dig displays it
```

And that's a **real DevOps-relevant observation** from your output.

One last thing: the `56` immediately after `google.com.`:

```text
google.com. 56 IN SOA
```

is the **TTL (Time To Live)** of this cached SOA answer at your resolver. It is **not** the SOA's `MINIMUM` value.

So don't confuse these two:

```text
56  → current cached TTL for this answer

60  → SOA MINIMUM / negative-caching-related value
```

That's the complete SOA record from **your actual command**.

Yes, let's slow down. These four are the confusing part of SOA because they describe **what secondary DNS servers do** and **how DNS handles a name that doesn't exist**.

We'll use one simple example throughout.

Suppose Google has:

```text
Primary DNS server:
ns1.google.com

Secondary DNS server:
ns2.google.com
```

The secondary has a copy of Google's DNS zone.

### Refresh — "When should I check for an update?"

Your SOA says:

```text
REFRESH = 900 seconds
```

That's **15 minutes**.

So the secondary DNS server basically does:

```text
ns2.google.com
      ↓
"Has the zone changed?"
      ↓
Check ns1.google.com
```

It does this approximately every **15 minutes**.

For example:

```text
10:00 → check
10:15 → check
10:30 → check
10:45 → check
```

If the zone hasn't changed, nothing needs to happen.

If the zone has a newer **SERIAL**, the secondary knows it needs to update its copy.

So remember:

> **REFRESH = How often should the secondary check the primary for changes?**

---

### Retry — "What if my check failed?"

Now imagine the secondary checks the primary at 10:00:

```text
ns2 → ns1
"Do you have a newer zone?"
```

But `ns1.google.com` doesn't respond.

Maybe there's a temporary network problem.

The SOA says:

```text
RETRY = 900 seconds
```

So the secondary waits **15 minutes** and tries again.

```text
10:00 → check → FAILED

10:15 → try again
```

If it fails again:

```text
10:30 → try again
10:45 → try again
...
```

So:

> **RETRY = If the refresh check fails, how long should I wait before trying again?**

The difference is therefore very simple:

```text
REFRESH = normal check interval

RETRY   = wait time after a failed check
```

---

### Expire — "How long can I keep using my old copy?"

This one is slightly different.

Imagine `ns2` has a copy of Google's zone.

Then `ns1` becomes unreachable.

`ns2` keeps trying:

```text
ns2 → ns1 ❌
ns2 → ns1 ❌
ns2 → ns1 ❌
```

How long can `ns2` continue using the old zone data?

Your SOA says:

```text
EXPIRE = 1800 seconds
```

That's **30 minutes**.

So, conceptually:

```text
Primary unreachable
       ↓
Secondary keeps trying
       ↓
30 minutes pass without successful refresh
       ↓
Secondary should stop treating that old zone data as valid
```

So:

> **EXPIRE = How long can a secondary continue using its old zone data if it cannot refresh it from the primary?**

---

### Now the fourth one: Negative caching

This one is **not about secondary DNS servers**.

This is about a DNS answer saying:

> **"That name does not exist."**

Suppose you ask:

```bash
dig abc123.google.com
```

Imagine Google DNS responds:

```text
NXDOMAIN
```

**NXDOMAIN = Non-Existent Domain**

It means:

```text
abc123.google.com
        ↓
does not exist
```

Now imagine thousands of users keep asking:

```text
"Does abc123.google.com exist?"
```

If the resolver asked Google's authoritative DNS every single time, that would be unnecessary.

So the resolver can temporarily remember:

```text
abc123.google.com → DOES NOT EXIST
```

That's called **negative caching**.

Your SOA has:

```text
60
```

which is relevant to the negative caching TTL.

So the resolver can remember the negative answer for the applicable period rather than immediately asking the authoritative server again.

---

### Put all four together

Your Google SOA:

```text
974394558  900  900  1800  60
              ↑    ↑    ↑    ↑
              │    │    │    │
           Refresh Retry Expire Negative
                            caching
```

Think technically:

```text
REFRESH
"Check for changes every 15 minutes."

RETRY
"If the check fails, try again after 15 minutes."

EXPIRE
"If I cannot successfully refresh for the allowed
 period, my old zone data eventually expires."

NEGATIVE CACHING
"If DNS says a name doesn't exist, that negative
 answer can be cached for the applicable period."
```

**The key distinction you should remember right now:**

```text
REFRESH + RETRY + EXPIRE
        ↓
Secondary DNS / zone synchronization

NEGATIVE CACHING
        ↓
Resolver remembering "this DNS name doesn't exist"
```

Don't worry about memorizing the exact Google values yet. **Understanding what each one does is more important.**
