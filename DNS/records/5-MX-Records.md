Let’s properly learn MX records
📧 MX Record — Mail Exchange
The easiest way to remember it:
MX tells the internet which mail server is responsible for receiving email for a domain.
Suppose you have:
shaker@company.com
Someone sends an email to that address.
The sender's mail server needs to discover:
“Where should I deliver email for company.com?”
It asks DNS for the MX record.
Example
company.com    MX    mail.company.com

This means:
company.com
     ↓
Which server handles email?
     ↓
mail.company.com

But notice something important:
MX contains a hostname, not an IP address.
So DNS then needs to resolve:
mail.company.com    A    10.0.1.20

Now we have:
Email
   ↓
user@company.com
   ↓
MX lookup
   ↓
mail.company.com
   ↓
A/AAAA lookup
   ↓
10.0.1.20
   ↓
Mail server

🔑 Why doesn't MX directly contain an IP?
Because DNS separates responsibilities.
MX:
“Which hostname handles mail?”
A/AAAA:
“What IP does that hostname resolve to?”
This is similar to what you've already learned with CNAME.

⭐ MX Priority
Here's where MX becomes slightly more interesting.
A domain can have multiple MX records:
company.com    MX    10    mail1.company.com
company.com    MX    20    mail2.company.com

The number is the priority.
Lower number = higher priority.
So:
10 → mail1.company.com  ← preferred
20 → mail2.company.com  ← backup

If mail1 cannot accept the email, the sending mail server can try mail2.
This is a very important real-world concept.
Mental model
Think of it like emergency contacts:
Priority 10 → First person to call
Priority 20 → Second person
Priority 30 → Third person

So remember:
MX = Who receives my email?
 MX priority = Who should be tried first?
That's enough for the core MX concept.
When you're ready, I'll give you one complete MX test with all questions together, just like you wanted for NS.


Great. Let’s test MX all at once.
MX Record — Test
1. What is the main purpose of an MX record?
A) Map a hostname to an IPv4 address
 B) Identify the mail server responsible for receiving email
 C) Identify the authoritative DNS server
 D) Map a hostname to an IPv6 address
2. What does this mean?
company.com  MX  mail.company.com

A) company.com has IP mail.company.com
 B) mail.company.com is the mail server for company.com
 C) company.com is an alias of mail.company.com
 D) mail.company.com is the authoritative DNS server
3. Does an MX record normally contain an IP address directly?
A) Yes
 B) No
4. Consider:
company.com  MX  10  mail1.company.com
company.com  MX  20  mail2.company.com

Which server has higher priority?
A) mail1.company.com
 B) mail2.company.com
 C) Both have equal priority
 D) Cannot determine
5. Why can a domain have multiple MX records?
A) To provide alternative mail servers/failover
 B) To provide multiple IP versions
 C) To replace the NS records
 D) To increase DNS TTL
Send all 5 answers together, e.g. 1B 2B 3B 4A 5A.
