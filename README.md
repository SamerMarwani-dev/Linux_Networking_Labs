🌐 DNS Troubleshooting Lab: SadServers "Tied in a Knot"
🎯 Scenario Goal
The objective of this lab was to diagnose and fix a broken DNS server running Knot DNS that was preventing internal users from resolving and accessing blog.sadservers.internal and api.sadservers.internal.

🔍 Step 1: Investigation & Diagnosis
Checking DNS Status: To identify why the domain wasn't resolving, I ran the dig command against the local DNS server.

Bash
dig blog.sadservers.internal
[1.png]

Finding: The server returned a status of SERVFAIL along with an Extended DNS Error (EDE: 24 (Invalid Data)). This indicated that while the Knot DNS service was active, its database configuration (Zone file) contained syntax errors preventing it from loading.

Inspecting the Zone File:
I located the active zone files directory at /var/lib/knot/zones/ and viewed the contents of the database.

Bash
cat /var/lib/knot/zones/sadservers.internal.zone
[2.png]

🛠️ Step 2: Isolating the Bugs
By deeply analyzing the syntax of the zone file, I isolated three critical infrastructure issues:

Misspelled Record Type: The record for the blog was written as CNAM instead of the standard CNAME.

Missing Trailing Dot: The alias target www.sadservers.internal lacked the crucial trailing dot (.), which caused the DNS server to treat it as a relative path rather than a Fully Qualified Domain Name (FQDN).

Incorrect/Missing Routing IPs: The www A record was mapped to a wrong placeholder IP (198.51.100.99), and the api record was entirely missing from the database.

🚀 Step 3: Resolution & Implementation
Syntax Fixes & Intermediate Check:
Using sudo nano, I corrected the spelling to CNAME and added the trailing dot (.) to anchor the domain properly. At this intermediate stage, the www was still pointing to the old IP.

[4.png]

Network Realignment (Mapping True Container IPs):
To discover where the actual services were running, I audited the server's network interfaces using:

Bash
ip address
[6.jpg]

Discovery: The backend services were running inside Docker containers bound to loopback/bogon IP addresses (203.0.113.10 for the web/blog frontend and 203.0.113.20 for the API).

Applying Database Transactions & Updating Serial:
To ensure the changes synced cleanly with Knot DNS's binary cache database, I used the native transaction engine (knotc zone-begin/commit) and incremented the SOA Serial Number to force downstream caching zones to refresh their data.

[9.png]

🧪 Step 4: Verification
After executing a final sudo knotc reload, I tested the connectivity and routing stack:

DNS Validation: Running dig confirmed a clean NOERROR status, accurately mapping the alias to the target backend IP.

[5.png]

Application Layer Validation: Testing connectivity with curl successfully fetched the active service header for the blog, while isolating the API state.

[8.png]

💡 Key Takeaways & Lessons Learned
DNS Syntax is Unforgiving: A single missing dot (.) turns an absolute path into a relative one, causing recursive lookups to loop back onto themselves and crash with SERVFAIL.

Transactional Database Management: Modern DNS servers (like Knot and BIND) handle zones like databases. Modifying text configurations manually requires clean invalidation or structural API updates (zone-begin/commit) to fully sync state.

Decouple Infrastructure Layers: Always troubleshoot down the OSI model—ensure core packet routing and port bindings (ip address, netstat) match your application layer expectations (DNS records) to isolate failures effectively.