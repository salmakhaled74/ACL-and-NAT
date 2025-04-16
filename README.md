![WhatsApp Image 2025-04-16 at 11 35 09 AM](https://github.com/user-attachments/assets/95810bb3-eefd-4005-9968-a9e7a60af485)

Controlled Internet Access with Firewall Filtering
🏢 Company: TechNova Solutions – Securing Internet Connectivity
As part of TechNova's ongoing network segmentation and security hardening, a firewall has been implemented to regulate internet access across departments. The company's edge router is now connected to an ISP to provide internet connectivity, but not all departments are allowed unrestricted access.

📌 Updated Requirements:
🌐 Only the IT Department (192.168.2.0/24) is allowed to access the internet.

🚫 All other departments — Sales (192.168.3.0/24) and Finance (192.168.1.0/24) — must be blocked from internet access.

🔐 The firewall (or the router acting as a firewall) will inspect and enforce this policy based on source IP addresses.

✅ All internal inter-departmental communication rules from before remain unchanged.

🧱 Network Placement Assumption:
csharp
Copy
Edit
             [Internet]
                 |
             [Edge Router] ← Firewall rules applied here
                 |
            -----------------
           |        |        |
         R0        R1       R2
       (Finance) (Sales)   (IT)
Edge Router is connected to all three internal routers (R0, R1, R2) and serves as the gateway to the internet.

You can either place a dedicated firewall or configure ACLs on the Edge Router to act as a firewall.

🔐 Access Control Implementation on Edge Router:
We'll use standard ACLs because we only need to match on the source IP of internal LANs.

✅ Allow IT (R2) to Access the Internet:
bash
Copy
Edit
access-list 130 permit 192.168.2.0 0.0.0.255
access-list 130 deny 192.168.1.0 0.0.0.255
access-list 130 deny 192.168.3.0 0.0.0.255
access-list 130 permit any   ! (optional - if you want other external traffic allowed)
📍 Apply the ACL inbound on the internal-facing interface of the Edge Router:
bash
Copy
Edit
interface GigabitEthernet0/1   ! <-- Example, internal interface
 ip access-group 130 in
This ensures that only the IT Department's traffic is forwarded to the internet, while traffic from other departments is silently dropped.

🔍 Verification Checklist:

Department	Internet Access	Allowed?
IT (192.168.2.0)	✅ Yes	✅ PASS
Finance (192.168.1.0)	🚫 No	✅ BLOCKED
Sales (192.168.3.0)	🚫 No	✅ BLOCKED
✍️ Want to Get Fancy?
Add logging to denied entries for monitoring:

bash
Copy
Edit
access-list 130 deny 192.168.1.0 0.0.0.255 log
access-list 130 deny 192.168.3.0 0.0.0.255 log
Use a named ACL for readability.

Insert a real firewall device like Cisco ASA or a Zone-Based Firewall on the edge router.
