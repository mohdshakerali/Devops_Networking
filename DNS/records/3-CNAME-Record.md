this is the important "why" behind CNAME.
The point isn't to create another IP. The point is to give a resource another useful name.
Simple real-world example
Imagine your actual application is:
app.company.com
      ↓
10.0.1.20

But you want users to access it as:
www.company.com

Instead of creating another A record:
www.company.com → 10.0.1.20

you can say:
www.company.com
      ↓ CNAME
app.company.com
      ↓ A
10.0.1.20

Now if tomorrow the application moves:
app.company.com
      ↓
10.0.2.50

you only change one place:
app.company.com → 10.0.2.50

www.company.com automatically follows it.
🔥 That's the real advantage
Without CNAME:
www.company.com → 10.0.1.20
api.company.com → 10.0.1.20

If the IP changes, you may need to update multiple records.
With CNAME:
www.company.com ──┐
                  ↓
              app.company.com
                  ↓
              10.0.1.20

Change the target once, and the aliases follow.
And this becomes VERY useful in cloud/DevOps
Suppose AWS gives your load balancer a hostname like:
my-load-balancer-123.aws....

You don't want users remembering that ugly AWS hostname.
You can have:
api.company.com
      ↓ CNAME
AWS load balancer hostname

So the user sees your clean domain, while the infrastructure can change underneath it.
So remember:
CNAME is mainly about aliases and abstraction — keeping the user's name stable while the actual destination can change.

So they can use www. Or app. Both will land to the same ip


For example:

www.company.com
       │
     CNAME
       ↓
app.company.com
       │
       A
       ↓
   10.0.1.20

So:

www.company.com → 10.0.1.20
app.company.com → 10.0.1.20

Both names can ultimately reach the same destination.

Just remember: www isn't directly storing the IP—the CNAME points it to app.company.com, and app.company.com has the A record.

That's the main purpose of CNAME.
