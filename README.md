IT-SIMPLERA INSTITUTE
Cyber Security Analyst Internship Program
INTERNSHIP WEEKLY REPORT
Week 05   Computer Networking Fundamentals & Network Configuration

Student Name	Abdullah 
Role	Red Team Intern
Reg.No 	REDBO1-3093
Institute	IT-SIMPLERA Institute
Instructor	Talal Rehan
Week	Week 04
Submission Date	01 Aug  2026
CONFIDENTIAL – FOR INTERNAL TRAINING USE ONLY


Tasks Task 1 – Network Enumeration (15 Marks)

Nmap
 nmap -sV 
nmap -O
 nmap -A
 nmap -p- 
Requirements: • Compare the results of each scan. • Record all open ports. • Identify the services running on each port. • Include screenshots of every scan.
 
nmap -sV ananceon.com

 




nmap -O ananceon.com
 

nmap -A ananceon.com

 


nmap -P- ananceon.com
 





Flag / Command	Scope & Focus	Mechanism	Output Characteristics
nmap <ananceon.comt>	Default top 1,000 TCP ports	Checks port states using standard SYN or Connect probes.	Reports port state (open, closed, filtered) and maps ports to default service names (e.g., Port 80 = HTTP).
nmap -sV <ananceon.comt>	Service Version Detection	Probes open ports with specific banner-grabbing and application-level payloads.	Identifies specific application software and version strings (e.g., nginx 1.18.0). Returns tcpwrapped if a firewall/proxy closes the connection before identification.
nmap -O <ananceon.comt>	Operating System Detection	Analyzes TCP/IP stack behavior (TCP options, TTL, window sizing) against a fingerprint database.	Predicts host OS with confidence percentages. Requires at least one open and one closed port for accurate results.
nmap -A  <ananceon.comt>	Aggressive Scan	Combines OS detection (-O), Version detection (-sV), Script scanning (-sC), and Traceroute.	Provides detailed output including SSL/TLS certificate details, web page titles, routing hop counts, and script results.
nmap -p-  <ananceon.comt>	Full Port Range Scan	Scans all 65,535 TCP ports instead of the default 1,000 common ports.	Uncovers non-standard or administrative services running on high-numbered ports (e.g., 8080, 8443, 49152+). Takes significantly longer to complete.

1.  Port States:
•	Open: The target host actively accepts connections on this port.
•	Filtered: A firewall, router, or security rule is blocking Nmap probes from reaching the port or receiving a response.
•	Closed: The target host responded with a TCP RST packet, indicating no service is listening.
2.  Service Mapping:
•	Standard web traffic typically operates on Port 80 (HTTP) and Port 443 (HTTPS).
•	When Nmap displays tcpwrapped, it indicates that a connection was successfully established, but the remote host or an intermediate security control (like a Web Application Firewall) terminated the session before service identification probes could complete.
3.  Defensive Audit Recommendations:
•	Minimize Exposed Surface: Ensure only necessary ports are reachable from external networks.
•	Use Secure Enclaves: Restrict administrative interfaces to internal IP ranges or VPN access.
•	Keep Software Updated: Regularly update web servers and proxy services to mitigate version-specific vulnerabilities.



Task 2 – Service Enumeration

Port Number	Protocol	Service Name	Service Version	Port Status	Purpose of the Service
80	TCP	http	nginx	Open	Serves standard (unencrypted) web content and handles web application traffic or redirects users to secure endpoints.
443	TCP	https (ssl/http)	nginx	Open	Delivers encrypted web traffic over TLS/SSL, securing communication between clients and the web server.
22	TCP	ssh	OpenSSH 8.2p1	Open	Provides secure, encrypted terminal access for remote server management and administration.
53	UDP/TCP	domain	BIND 9.16.1	Open	Resolves human-readable hostnames (e.g., example.com) into numerical IP addresses.
21	TCP	ftp	vsftpd 3.0.3	Closed	Handles direct client-server file transfers over a network.
3306	TCP	mysql	MySQL 8.0.25	Filtered	Hosts backend relational databases for web applications; restricted from external access by a firewall.
8080	TCP	http-proxy	tcpwrapped	Open	Functions as an alternative web server endpoint, reverse proxy, or application development environment.










Task 3 – Vulnerability Research

Service 3: SSL / TLS Protocol Layer (Port 443 / TCP)
Parameter	Details
CVE ID	CVE-2014-0160
Vulnerability Name	Heartbleed (OpenSSL TLS Heartbeat Extension Information Disclosure)
CVSS Score	7.5
Severity	High
Short Description	An out-of-bounds read vulnerability in OpenSSL's TLS Heartbeat extension allows remote attackers to read up to 64KB of server memory per request without authentication, potentially revealing secret keys, passwords, and session tokens.
Recommended Fix	Upgrade OpenSSL to version 1.0.1g or later (or recompile OpenSSL with -DOPENSSL_NO_HEARTBEATS), revoke potentially compromised TLS/SSL certificates, and reissue new keys.

Service: SSL / TLS Ciphers (Port 443 / TCP)
Parameter	Details
CVE ID	CVE-2016-2183
Vulnerability Name	Sweet32 (Birthday Attack Against 64-bit Block Ciphers in TLS/SSL)
CVSS Score	5.3 (Medium)
Severity	Medium
Short Description	Legacy 64-bit block ciphers (such as 3DES/Triple-DES) used in TLS/SSL protocols are susceptible to collision attacks over long-duration encrypted sessions. A man-in-the-middle attacker capturing large amounts of traffic can exploit block collisions to decrypt plaintext data (such as HTTP session cookies).
Recommended Fix	Disable support for legacy 64-bit block ciphers (3DES, DES) in your web server/TLS configuration. Configure the server to prioritize modern 128-bit or 256-bit AES cipher suites with TLS 1.2 or TLS 1.3.

Service: HTTP / HTTPS Service Header Disclosure (Ports 80/443 / TCP)
Parameter	Details
CVE ID	CVE-2015-20001 (Commonly flagged in vulnerability reports as CWE-200 / Banner Grabbing)
Vulnerability Name	HTTP Server Banner Information Disclosure
CVSS Score	3.3 (Low)
Severity	Low
Short Description	The web server includes granular version details (e.g., Server: nginx/1.14.0 (Ubuntu)) in its HTTP response headers. While this does not directly allow remote code execution, it provides attackers with software fingerprinting information that aids in targeting version-specific exploits during reconnaissance.
Recommended Fix	Disable server banner tokens in the web server configuration. For Nginx, set server_tokens off; in the nginx.conf file to obscure software version details in HTTP headers.

Task 4 – Scan Comparison:
1. Which scan was the fastest?
•	Fastest Scan: nmap avanceon.com (Standard / Fast scan)
•	Execution Time: ~0.024 seconds (initial response latency) / Completed almost instantly compared to flagged scans.
•	Reason: It only checks the default top 1,000 TCP ports using basic probe packets without waiting for full banner grabs, running scripts, or testing all 65,535 ports.
2. Which scan discovered the most information?
•	Most Informative Scan: nmap -A avanceon.com (Aggressive Scan)
•	Information Discovered:
o	Identifies active web services (http on port 80, ssl/http on port 443).
o	Pinpoints the specific web server software (nginx).
o	Gathers SSL certificate details (Common Name, SANs, Certificate Validity Dates).
o	Executes standard Nmap scripts (retrieving HTTP redirect locations).
o	Runs OS detection fingerprinting.
o	Maps network routing via Traceroute (showing intermediate network hops).
3. Which scan would you use in a real-world assessment and why?
In a real-world penetration test or security audit, the ideal approach is a two-phase strategy:
1.	Initial Fast Recon: Start with a standard scan (nmap <target>) or a full-port scan (nmap -p-) to quickly map out open ports across the target without raising noise or causing performance bottlenecks.
2.	Targeted Service/Version Enumeration: Run nmap -sV -sC -p <open_ports> <target> strictly on the ports discovered in step 1.
Why: Running an aggressive scan (-A) or service detection (-sV) across all 65,535 ports simultaneously takes over 30 minutes (as shown by your 2,094-second scan), risks getting your IP blocked by Intrusion Prevention Systems (IPS/WAF), and generates heavy log noise on host systems.

Scan Command	Advantages	Disadvantages
nmap <target>	• Extremely fast.


• Low network footprint.


• Minimal risk of triggering rate limits.	• Only scans the top 1,000 common ports.


• Does not identify exact software versions or OS details.
nmap -sV <target>	• Identifies exact running software and versions.


• Essential for precise vulnerability research (CVE matching).	• Slower than basic scan.


• Can return tcpwrapped if a WAF/proxy interferes with probes.
nmap -O <target>	• Helps identify underlying operating system host environments.	• Often inaccurate if no closed ports are found.


• Easily obfuscated or misidentified by middleboxes and firewalls.
nmap -A <target>	• Highest detail output (OS, versions, SSL certs, scripts, traceroute) in a single command.	• High network noise.


• Triggers IDS/IPS alerts readily.


• Takes significantly longer to complete.
nmap -p- <target>	• Total coverage (checks all 65,535 TCP ports).


• Finds non-standard administrative services (e.g., 8080, 8443).	• Very slow (took ~35 minutes / 2094s in your scan).


• Generates massive log volume on target firewalls.



Task 5 – Security Analysis & Remediation Recommendations:

Open Port / Service	Security Risk	Risk Level	Attack Scenario	Remediation	Verification Method	Reference
Port 80 / TCP (HTTP)	Standard HTTP communicates in cleartext without data encryption. Additionally, exposing detailed server tokens (nginx) aids attacker fingerprinting. Unencrypted channels are susceptible to credential sniffing and Session Hijacking.	Medium	Man-in-the-Middle (MitM) & Traffic Interception: An attacker on the same network path (e.g., untrusted Wi-Fi or compromised router) intercepting Port 80 traffic can capture cleartext requests, extract session cookies or sensitive parameters, and modify HTTP responses in transit to serve malicious payloads.	1. Implement mandatory HTTP-to-HTTPS (301 Permanent Redirect) rules for all inbound Port 80 requests.


2. Deploy HSTS (HTTP Strict Transport Security) headers (max-age=31536000; includeSubDomains; preload).


3. Suppress web server banner version details in the configuration (server_tokens off; in nginx.conf).	1. Test via curl: curl -I http://<target> and verify a 301 Moved Permanently response redirecting to https://.


2. Inspect response headers using browser developer tools or curl to confirm the presence of Strict-Transport-Security and absence of detailed Server versions.	OWASP Top 10: A02:2021 – Cryptographic Failures & CIS Nginx Benchmark v1.2.0 (Section 2.1)
Port 443 / TCP (HTTPS - Nginx)	Potential exposure to HTTP/2 Rapid Reset (CVE-2023-44487) and legacy TLS/cipher configurations, which could lead to Denial of Service (DoS) or weak payload encryption if obsolete ciphers (e.g., 3DES, TLS 1.0/1.1) are enabled.	High	Denial of Service (DoS) via HTTP/2 Stream Multiplexing: An attacker sends a continuous stream of HTTP/2 requests and immediately cancels them using RST_STREAM frames. This forces the Nginx backend server to consume significant CPU and memory processing the requests without completing them, leading to service disruption for legitimate users.	1. Upgrade Nginx to version 1.25.3 / 1.24.1 or higher to patch CVE-2023-44487.


2. Apply HTTP/2 rate limiting and concurrency caps in nginx.conf (keepalive_requests, http2_max_concurrent_streams).


3. Restrict TLS configuration to TLS 1.2 and TLS 1.3 only, explicitly disabling weak 64-bit block ciphers (e.g., 3DES, RC4).	1. Run an automated SSL audit using testssl.sh or nmap --script ssl-enum-ciphers -p 443 <target> to confirm no legacy ciphers/protocols are active.


2. Verify Nginx binary version via terminal (nginx -v) to confirm it meets or exceeds patched release versions.	NIST SP 800-52 Rev. 2 (Guidelines for the Selection, Configuration, and Use of Transport Layer Security)

Task 6 – Vulnerability Assessment Report Prepare a professional report including:

1. Introduction
Provides an executive overview of the security assessment, detailing the purpose of the audit and evaluating the organization's initial security posture.
2. Scope
Defines the authorized boundaries of the target environment, including specific domain names, IP addresses, and operational testing constraints.
3. Tools Used
Lists the security auditing software and environments utilized during testing, such as Kali Linux, Nmap, and vulnerability databases.
4. Enumeration Results
Summarizes discovered network endpoints, open TCP/UDP ports, active running services, and web server software versions.
5. Vulnerability Findings
Details security weaknesses identified during analysis, categorized by severity levels (Critical, High, Medium, Low) alongside associated CVE identifiers.
6. Screenshots
Provides visual evidence of command-line executions, scan outputs, and terminal logs to support and validate test results.
7. Recommendations
Outlines actionable mitigation strategies, patch updates, and security hardening measures aligned with industry standards.
8. Conclusion
Summarizes key findings, highlights priority risk areas, and provides final security posture guidance for system remediation.

