
📝 TXT Record
First: What does TXT stand for?
TXT = Text
Unlike:
A → IPv4 address
AAAA → IPv6 address
CNAME → another hostname
MX → mail server hostname
NS → authoritative name server
TXT stores text information in DNS.
But the important question is:
Why would we want to put text in DNS?
That's where the real-world use comes in.

🏢 Analogy: Company building notice board
Imagine company.com is a company building.
Inside the building you have different things:
Reception
Mail room
Employees
Security
Notice board

We already learned:
NS record
"Which reception desk is responsible for this building's DNS?"
MX record
"Which room handles incoming mail?"
Now imagine there's a public notice board outside the building.
Anyone can look at it.
The company can put information on that notice board such as:
"This building is verified by Google."
or
"These are the authorized mail senders for this company."
That DNS notice board = TXT record.

1️⃣ TXT for domain verification
This is one of the easiest ways to understand TXT.
Suppose you own:
company.com

You want to use some service, say:
Google

Google wants to make sure:
"Do you actually control company.com?"
They could tell you:
Add this TXT record to your DNS:
company.com TXT "google-site-verification=ABC123"

You add it.
Now Google checks DNS:
Google
   ↓
DNS lookup
   ↓
company.com
   ↓
TXT?
   ↓
google-site-verification=ABC123

Google sees the expected value.
Therefore:
"This person can modify the DNS for company.com, so they probably control the domain."
🔑 Mental model
TXT + verification = proving domain ownership/control

2️⃣ TXT for SPF
Now let's understand SPF rather than just throwing the abbreviation at you.
SPF = Sender Policy Framework
Imagine your company has:
company.com

You use Google Workspace for email.
Your legitimate emails should come from Google's mail servers.
But an attacker could try to send:
From: ceo@company.com

even though they aren't actually using your company's mail infrastructure.
So you publish a DNS TXT record saying:
"These are the servers/services allowed to send email for my domain."
For example:
company.com TXT "v=spf1 include:_spf.google.com ~all"

A receiving mail server can look up this TXT record and use it as part of its SPF check.
Analogy
Think of a company security guard.
The company gives the guard a list:
AUTHORIZED:
Google mail servers

Someone arrives saying:
"I'm from company.com."
The guard checks whether the sender is coming through an authorized route.
That's essentially the idea behind SPF.
🔑 Mental model
SPF = Which mail senders are authorized to send for my domain?
And SPF is published using TXT records.

3️⃣ DKIM
DKIM = DomainKeys Identified Mail
This one is a little different.
Imagine your company sends an email.
You want the receiver to have a way to verify:
"Did this email really come from an authorized system for this domain, and was the message altered?"
DKIM uses cryptographic signatures.
The sending mail system signs the email using a private key.
The corresponding public key is published in DNS.
That public key is commonly stored in a TXT record.
For example:
selector1._domainkey.company.com TXT "v=DKIM1; k=rsa; p=PUBLIC_KEY..."

Don't worry about the cryptography yet.
Just understand the flow:
Mail server
    ↓
Signs email with private key
    ↓
Email sent
    ↓
Receiving mail server
    ↓
Looks up DKIM public key in DNS
    ↓
Verifies signature

Analogy
Think of sending a document with a special company seal.
The sender puts the seal on it.
The receiver has access to the official information needed to check that seal.
DNS publishes the information needed for that verification.
🔑 Mental model
DKIM = Verify that an email carries a valid cryptographic signature associated with the domain.

4️⃣ DMARC
DMARC = Domain-based Message Authentication, Reporting, and Conformance
This name looks scary 😂, but the basic idea is simple.
SPF and DKIM help with authentication.
DMARC tells the receiving mail system:
"What should you do if an email claiming to be from my domain fails authentication checks?"
For example:
_dmarc.company.com TXT "v=DMARC1; p=quarantine"

The important part here:
p=quarantine

means roughly:
Treat failing messages suspiciously / place them in quarantine, depending on the receiver's implementation.
Other policies include:
p=none
p=quarantine
p=reject

We'll go deeper into these later when we study email security.
Analogy
Think of a security guard again.
SPF/DKIM help answer:
"Is this person actually authorized?"
DMARC adds:
"Okay, if they're NOT authorized, what should I do with them?"
That's the key idea.

🧠 Put all four together
This is the part I want you to remember:
                   DNS TXT
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
     Verification     SPF        DKIM/DMARC
          │            │            │
   "Do you control    "Who can     "How should
    this domain?"      send mail?"   mail be verified/
                                      handled?"

And importantly:
TXT doesn't inherently mean SPF, DKIM, or DMARC.
TXT is the DNS record type.
SPF, DKIM, DMARC, and verification systems are uses of the TXT record.
That's a very important distinction.

🔥 Compare everything you've learned
A
app.company.com
        ↓
192.168.1.10

AAAA
app.company.com
        ↓
IPv6 address

CNAME
www.company.com
        ↓
app.company.com

NS
company.com
        ↓
authoritative DNS server

MX
company.com
        ↓
mail.company.com

TXT
company.com
        ↓
text information
        ↓
verification / SPF / etc.

One sentence to lock it in:
TXT doesn't tell you where a server is; it publishes information that other systems can read and interpret.
Now TXT itself should be clear before we test it. We can then separately go deeper into SPF, DKIM and DMARC only to the level you actually need for DevOps, rather than dumping email-security theory on you.
