#FIREWALL RULE REVIEW

    • identifying overly permissive, risky, unused, or misaligned rules by analyzing policy attributes
    • Each rule is reviewed using the following core parameters:
    • Source Zone / Interface
    • Destination Zone / Interface
    • Source Address
    • Destination Address
    • Service / Port
    • Action (Allow/Deny)
    • Security Profiles / Inspection
    • Logging & Hit Count


1. determine traffic direction:
Inbound (External → Internal)
Outbound (Internal → External)
Inter-zone / Lateral
Inbound rules generally carry higher risks.
Outbound rules focus more on data exfiltration and command-and-control risks.

2. Source & Destination Scope
any / all / large subnets (e.g., 0.0.0.0/0)
Broad object groups
Narrow scope → Lower risk
Broad scope → Higher risk

3. Service / Port
Must be specific (http, tcp/443)
Not broad (any/all, tcp-all)

4. Also consider protocol risk:
High-risk services: RDP, SMB, SSH, FTP, Telnet, Database ports, LDAP, etc.
Web services behind reverse proxy/WAF are lower risk than direct exposure

5. Check whether rule has Security Profiles & Controls (IPS/AV/WAF/SSL-INSPECTION, VPN, MFA, IP RESTRICTIONS)

6. Hit Count & Logging
Active Use (>1000 hits typical business traffic)
Low Use (<10 hits or rare usage (validate necessity))
Unused (0 hits)
Zero Hit → Candidate for cleanup
Very high hits → Business-critical; ensure tightly scoped

7. Logging disabled → Visibility gap

##Firewall Rule Review – Severity Matrix

| **Source**        | **Destination**    | **Service**                     | **Security Profiles**| **Hit Count** | **Exposure Category**                | **Severity** |
|-------------------|--------------------|---------------------------------|----------------------|---------------|--------------------------------------|--------------|
| ANY (Internet)    | ANY                | ANY                             | None                 | Active Use    | Unrestricted External Exposure       | High         |
| ANY (Internet)    | Internal Network   | Sensitive (RDP/SSH/DB/SMB)      | None                 | Active Use    | Direct External Sensitive Exposure   | High         |
| ANY (Internet)    | Internal Server    | Sensitive                       | (IPS/AV present)     | Active Use    | Controlled External Access           | Medium       |
| ANY (Internet)    | DMZ Server         | HTTP/HTTPS                      | (IPS/AV present)     | Active Use    | Published Service                    | Medium       |
| Restricted        | ANY Internal       | Sensitive                       | None                 | Active Use    | Internal Broad Sensitive Exposure    | Medium       |
| Restricted        | ANY Internal       | Sensitive                       | (IPS/AV present)     | Active Use    | Controlled Internal Broad Access     | Low          |
| Restricted        | ANY Internal       | Specific Service                | (IPS/AV present)     | Active Use    | Broad but Limited Service            | Low          |
| Restricted        | Specific Server    | Sensitive                       | (IPS/AV present)     | Active Use    | Restricted Administrative Access     | Low          |
| Restricted        | Specific Server    | Specific Service                | (IPS/AV present)     | Active Use    | Least Privilege Access               | Informational|
| Restricted        | ANY Internal       | ANY                             | None                 | Active Use    | Internal ANY–ANY Exposure            | Medium       |
| ANY / Restricted  | ANY / Restricted   | ANY / Specific                  | Any                  | Unused        | Unused Rule                          | Informational|
| Restricted        | Specific Server    | Sensitive                       | (IPS/AV present)     | Low Use       | Limited Exposure                     | Low          |
| VPN Source        | Specific Server    | Sensitive                       | (IPS/AV present)     | Active Use    | Authenticated Remote Access          | Low          |

*Disclaimer: The severity matrix is provided for reference purposes only and represents a general risk guidance framework. It is not an exact or definitive classification model. Final severity should be determined based on detailed rule logic, network architecture, implemented security controls, and validated business requirements.*



Example 1:                                                                                                                                                             
| Rule Name           | From Zone      | To Zone          | Source                   | Destination    | Service        | Action | Status  | Security Profiles       | Hit Count |
|---------------------|----------------|------------------|--------------------------|----------------|----------------|--------|---------|-------------------------|-----------|
| INTERNAL-RDP-ACCESS | Internal-Users | Internal-Servers | USER_SUBNET_10.10.1.0/24 | FILE-SERVER-01 | RDP (TCP/3389) | ACCEPT | Enabled | AV, IPS, SSL-Inspection | 1483 (>0) |

• Traffic Direction> inside: internal-to-internal / trusted-zone traffic, not internet-facing
  Internal RDP exposure less risky than external RDP exposure.
  
• Source: USER_SUBNET_10.10.1.0/24
  Restricted private subnet, not ANY.
  Logic: Narrow source scope = controlled access.
  
• Destination: FILE-SERVER-01
  Single host or defined host object
  Logic: Specific destination is better than subnet or ANY.
  
• Service: RDP is a sensitive service, but we verified the scoping
  Action allowed (make sure properly scoped rest).
  
• Security profiles enabled significantly reduced exploitation risk.

• Hit count suggest it actively using.

• RULE LOGIC: This rule allows RDP access from a specific internal subnet to a specific internal system with multiple security profiles enabled.

• Severity: low or info if properly scoped



Example 2: 
| Rule Name           | From Zone | To Zone           | Source | Destination     | Service        | Action | Status  | Security Profiles | Hit Count |
|---------------------|-----------|-------------------|--------|-----------------|----------------|--------|---------|-------------------|-----------|
| INTERNET-SMB-ACCESS | Internet  | Internal-Servers  | ANY    | FILE-SERVER-01  | SMB (TCP/445)  | ACCEPT | Enabled | None              | 210 (>0)  |

• Traffic Direction:
Internet(any) → Internal
Direct external exposure of internal system.

• Source
ANY
Logic: Unrestricted source.

• Destination
Specific internal server
Logic: Internal file server directly reachable.

• Service
SMB is a sensitive file-sharing service.

• Security Profiles: None enabled.

• Hit Count:  Actively used.

• RULE LOGIC
This rule allows direct SMB access from the internet to an internal file server without inspection or compensating controls.

• Severity: High


Example 3: 
| Rule Name           | From Zone | To Zone        | Source | Destination    | Service         | Action | Status  | Security Profiles | Hit Count  |
|---------------------|-----------|----------------|--------|----------------|-----------------|--------|---------|-------------------|------------|
| INTERNET-WEB-ACCESS | Internet  | Internal-Web   | ANY    | WEB-SERVER-01  | HTTPS (TCP/443) | ACCEPT | Enabled | IPS, AV           | 18200 (>0) |

• Traffic Direction: Internet → Internal

• Source: ANY

• Destination: Specific internal web server.

• Service: HTTPS web service.

• Security Profiles: IPS and AV enabled.

• Hit Count: Actively used.

• RULE LOGIC
Publishes a web service directly to an internal server from the internet with inspection controls enabled.

