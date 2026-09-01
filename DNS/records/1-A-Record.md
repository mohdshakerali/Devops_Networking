IPv4 is very commonly configured in DevOps, but there are two different things to keep separate:
Configuring an IPv4 address on infrastructure
Creating an A record in DNS
They're related, but they're not the same thing.
1. Infrastructure gets an IPv4 address
For example, an EC2 instance might have:
EC2
 ↓
Private IPv4
10.0.2.15

Or a load balancer/network interface may have IPv4 connectivity.
You configure/design IPv4 when you're planning things like:
VPC
 ├── 10.0.0.0/16
 │
 ├── Public subnet
 │    └── 10.0.1.0/24
 │
 └── Private subnet
      └── 10.0.2.0/24

This is the networking/IP-addressing side.

2. Then DNS can point a name to that IPv4
Suppose your application is reachable at:
10.0.2.15

You may create:
api.company.com    A    10.0.2.15

Now users/applications can use:
api.company.com

instead of remembering the IP.
So the relationship is:
Infrastructure
     │
     │ has IPv4
     ▼
10.0.2.15
     │
     │ DNS A record
     ▼
api.company.com

Where you'll see this in DevOps
AWS:
VPC
 ↓
Subnet
 ↓
EC2
 ↓
Private/Public IPv4
 ↓
A record in Route 53

Docker:
Container
 ↓
Private IPv4

Kubernetes:
Pods and nodes use IP addressing, while Kubernetes DNS provides names for services.
Terraform:
You may configure the network/IP architecture and create DNS records as code.

🔥 But don't think:
"DevOps engineers configure IPv4 whenever they create an A record."
No.
Usually the infrastructure/networking layer determines the IP, and DNS maps a name to it.
IP addressing = "Where is the resource?"

DNS A record = "What name should I use to find that IPv4 address?"

And this distinction will become very important when we get into AWS VPC + Route 53, because you'll see both sides working together.


Think of a hotel. 🏨
Hotel Name = "Grand Hotel"

The hotel has multiple buildings:
Grand Hotel
    │
    ├── Building A → Address 10.0.0.10
    ├── Building B → Address 10.0.0.11
    └── Building C → Address 10.0.0.12

Now map that to DNS:
Grand Hotel      = Hostname (google.com)
Building         = Server
Building address = IPv4 address
Directory        = DNS

The DNS directory says:
google.com → 10.0.0.10
google.com → 10.0.0.11
google.com → 10.0.0.12

So when someone asks:
"Where is Google?"
DNS can give them multiple addresses, just like a hotel name can refer to multiple buildings.
🧠 Remember this
Hostname = Name of the place
 IPv4 = Address of the place
 A record = Directory entry connecting the name to the address

