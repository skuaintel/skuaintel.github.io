**Date:** 2026-03-23  
**Author:** skuaintel

# Research Note 001: Infrastructure Analysis of example.com

This investigation examines the infrastructure behind the domain example.com so we can understand its hosting, ownership and TLS configuration.  
The goal is for us to demonstrate a simple methodology for domain level investigation using tools commonly found. 

## Method
The following techniques were used:

- DNS lookup using `dig`
- WHOIS analysis
- IP ownership investigation
- TLS inspection

## Findings

### Domain Lookup example.com
A DNS lookup using `dig` shows the domain resolves to the following IP addresses:
- 104.18.26.120  
- 104.18.27.120 

These IPs fall within Cloudflare-owned address space, indicating that traffic to this domain is routed through Cloudflare’s CDN rather than directly to an origin server. This is a common setup used to provide performance benefits and to obscure backend infrastructure.

<img width="560" height="344" alt="dig-on-example-com" src="https://github.com/user-attachments/assets/a8eda2bb-b42f-457b-9025-9a03e0528344" />

We can also check nslookup on example.com to see the same results.

<img width="484" height="212" alt="nslookup-on-example-com" src="https://github.com/user-attachments/assets/807b9883-595a-4982-8f09-c68e93fb0742" />

### WHOIS (Domain Info) example.com
Registrar: RESERVED-Internet Assigned Numbers Authority (IANA)  
Name servers: ELLIOT.NS.CLOUDFLARE.COM, HERA.NS.CLOUDFLARE.COM  
Registered on: 08/14/1995 at 04:00  

This indicates the domain is reserved and not owned by a typical organisation, confirming it is used for documentation/testing.

<img width="756" height="331" alt="whois-domain-example-com" src="https://github.com/user-attachments/assets/75bff6ba-fb73-4fda-b9bf-95ed90e2b00e" />


### WHOIS (IP Information) example.com
Organisation: Cloudflare, Inc (CLOUD14)  
ASN: ASN information wasn't returned in this query, however the IP range (104.16.0.0/12) is known to belong to Cloudflare.  
Network range: 104.16.0.0 - 104.31.255.255 (104.16.0.0/12)  

This confirms the domain is fronted by a CDN, meaning the real server location is hidden and traffic is proxied.

<img width="864" height="442" alt="IP-whois-example-com" src="https://github.com/user-attachments/assets/3bd91de5-a469-46f0-897c-96e1fdd36010" />


### TLS Certificate Check of example.com
#### Subject Alternative Name (SAN):
  - example.com (matched host)

<img width="598" height="19" alt="san-example-com" src="https://github.com/user-attachments/assets/fcbda4ac-335b-4eb5-aae9-f0138fcd4f6c" />


#### Issuer:
  - O=SSL Corporation
  - CN=Cloudflare TLS Issuing ECC CA 3

<img width="590" height="19" alt="issuer-example com" src="https://github.com/user-attachments/assets/dba2b8c9-26c5-4450-b27f-545980b132e7" />


#### Validity Period:
  - Start: Feb 13, 2026
  - Expiry: May 14, 2026

This indicates automated certificate management, which is typical of CDN-managed infrastructure such as Cloudflare.

<img width="395" height="44" alt="Validity-Period-example-com" src="https://github.com/user-attachments/assets/cc1d392f-d106-485c-9e20-e60f76f56ca0" />


#### Cryptography
Leaf Certificate (Level 0):
  - Key Type: EC (prime256v1 / P-256)
  - Signature Algorithm: ECDSA with SHA-256

Intermediate Chain:
  - Level 1: EC secp256v1 (ECDSA-SHA384)
  - Level 2: EC secp384r1 (SHA256 with RSA)
  - Level 3: RSA 2048 (SHA1 with RSA)

Modern ECC-Based certificate
Mixed chain (ECC and RSA root), common in public PKI

<img width="932" height="85" alt="SSL-Chain-Example-Com" src="https://github.com/user-attachments/assets/67b70ab8-67d9-48f5-9295-fc2b95b4fafb" />


#### TLS Protocol  
TLS Version: TLS 1.3  
Session Feature: NewSessionTicket observed  
ALPN: HTTP/2 negotiated  

TLS 1.3 is a modern secure configuration for handling data in transit.

<img width="452" height="116" alt="TLS-example-com" src="https://github.com/user-attachments/assets/a1b1b734-3dca-4bd5-b0ca-702f1c692fa1" />


### Certificate Transparency

Searching on [crt.sh](https://crt.sh/?q=example.com) for example.com we can see the following output. 

Key Findings:
  - Domain is actively fronted by Cloudflare
  - Uses automated, short-lived certificates
  - A multi-CA history shows infrastructure has changed over time, with possible migration between providers and is normal for long-lived domains.
  - Supports wildcard coverage across subdomains.

<img alt="crt-sh-example-com" src="https://github.com/user-attachments/assets/f3a77bdb-a79f-44ee-9806-99e9c7244b2f" />

The origin infrastructure is likely obscured, as Cloudflare terminates TLS connections and proxies traffic between clients and the backend server.

### Summary of Key Observations

  - The domain is fronted by Cloudflare infrastructure, obscuring origin hosting.
  - DNS, TLS, and IP data consistently indicate CDN-based delivery.
  - Short-lived certificates and TLS 1.3 suggest automated, modern configuration.
  - Historical certificate data indicates long-term use with evolving infrastructure.

These characteristics are consistent with professionally managed web infrastructure.

## Conclusion

The domain example.com is hosted behind Cloudflare infrastructure, which abstracts the origin server and provides CDN and TLS termination services.

The use of short-lived certificates, modern TLS (1.3), and Cloudflare-managed DNS indicates a standard configuration typical of professional web infrastructure.

This investigation demonstrates how publicly available data can be used to assess domain infrastructure and security posture, even when origin systems are intentionally obscured.







