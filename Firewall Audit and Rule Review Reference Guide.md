The following points are summarized from publicly available [firewall audit guidance](**https://pcidssguide.com/firewall-audit-checklist/#google_vignette**) and represent common areas reviewed during firewall audits:

## 1. Gather Key Firewall Information Before Starting Audit
- Obtain previous audit reports and existing network diagrams.
- Collect firewall logs, rule bases, and change history.
- Identify VPNs, ISPs, vendor/patch information, and critical servers.

## 2. Check Firewall Access Controls
- Verify formal access authorization processes.
- Check user/password policies and administrative accounts.
- Ensure only documented users have firewall access.

## 3. Review Firewall Change Management Process
- Changes should have documented approval, justification, and testing.
- Each rule change should be tied to a documented change request.
- Confirm rules include comments indicating author and purpose.

## 4. Evaluate Firewall Monitoring and Logging Processes
- Ensure logging is enabled and regularly reviewed.
- Audit logs should be retained and analyzed for trends.

## 5. Clean Up and Optimize Firewall Rule Base
- Identify unused, duplicate, or shadowed rules.
- Remove rules with zero hit count or no valid business need.
- Ensure cleanup and default deny rules exist and are placed appropriately.

## 6. Review Firewall Rule Base Organization
- Check firewall management access rules are restricted.
- Ensure rule naming conventions and comments improve readability.
- Validate that no services or sources are overly broad without justification.

## 7. Physical and System Security Controls
- Secure firewall operating system and hardware platforms.
- Keep firewall firmware and software up to date.

## 8. Restore and Recovery Processes
- Document and test firewall configuration backups.
- Validate recovery procedures periodically.

## 9. Continuous Assessment and Risk Resolution
- Conduct regular audit cycles (e.g., quarterly).
- Review firewall rules periodically to identify emerging risks.
- Ensure firewall configurations align with internal policy requirements.

## 10. Business and Compliance Context
- Assign business justification to firewall rules.
- Ensure the audit aligns with corporate risk policy and relevant compliance standards (e.g., PCI DSS).
  

 ** link: https://pcidssguide.com/firewall-audit-checklist/#google_vignette**

