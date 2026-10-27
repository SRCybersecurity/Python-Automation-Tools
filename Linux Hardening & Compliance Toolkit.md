# Linux Hardening & Compliance Toolkit
A lightweight Python-based Linux security auditing and hardening assessment tool designed to identify common system security issues and provide actionable remediation recommendations.
The tool performs a basic security assessment of Linux hosts, including SSH configuration checks, critical file permission validation, firewall availability detection, and security audit reporting.
 </br>

***Security Notice:*** 
This project is an auditing utility, not a complete Linux hardening framework or enterprise vulnerability scanner. Always validate findings against your organization's security baseline before applying remediation. </br>

***Key Features*** </br>

🔐 SSH Security Auditing </br>
Checks whether direct SSH root login is enabled. </br>
Identifies insecure PermitRootLogin configuration. </br>

📁 Critical File Permission Checks </br>
/etc/passwd </br>
/etc/shadow </br>
Compares current permissions against expected values. </br>

🔥 Firewall Detection </br>
Detects availability of nftables. </br>
Detects availability of iptables. </br>

🐧 Environment Detection </br>
Detects containerized environments. </br>
Skips systemd service auditing when running inside a container. </br>

🛡️ Rootkit Detection Status </br>
Reports whether a rootkit detection engine is configured. </br>
Currently serves as a placeholder for future integration. </br>

📊 ***Security Audit Summary*** </br>

**Total findings:**
Passed/failed checks
</br>
Severity classification
</br>
Individual recommendations

📁 ***Project Structure*** </br>
```text
Python-Automation-Tools/
└── 🔒 Linux-Hardening-and-Audit-Tool/
    └── 🐍 main.py
```

***Requirements***
Python 3.x 
Linux operating system
Appropriate permissions for reading security-sensitive system configuration. 
Some checks may require elevated privileges depending on the target environment. 

***Installation*** </br>
Clone the repository: </br>

     git clone https://github.com/<your-username>/<your-repository>.git 
     cd "Python-Automation-Tools/Linux Hardening and Audit Tool" 

***Run the audit tool:*** 

     '''   python3 main.py

For systems where certain security files or configuration require elevated privileges:
 
    '''  sudo python3 main.py

Run the tool only on systems you own or are explicitly authorized to audit.

***Example Output***

```text

======================================
Linux Security Audit Tool
======================================

Running security audit...


======================================
 SECURITY AUDIT SUMMARY
======================================

Total Findings: 4

Status:
    Passed:        2
    Failed:        1

Severity:
    Critical:      0
    High:          1
    Medium:        0
    Low:           2
    Info:          1

======================================

========== Firewall ==================
nftables: Available
iptables: Available

========== Services ==================
Container environment detected.
systemd service auditing skipped.

[HIGH] SSH Root Login
Status: FAIL
Current: PermitRootLogin yes
Expected: PermitRootLogin no
Recommendation: Disable direct SSH login for root.

[LOW] Permissions: /etc/passwd
Status: PASS
Current: 0o644
Expected: 0o644
Recommendation: No action required.

[LOW] Permissions: /etc/shadow
Status: PASS
Current: 0o640
Expected: 0o640
Recommendation: No action required.


[INFO] Rootkit Detection
Status: NOT_IMPLEMENTED
Current: No rootkit detection engine configured
Expected: Dedicated rootkit detection capability
Recommendation: Integrate an approved rootkit detection mechanism
before relying on this check.

===============================
Audit completed
===============================

***Security Checks***

1. SSH Root 
The tool evaluates the SSH server configuration for: PermitRootLogin

A configuration such as:
PermitRootLogin yes
is reported as a High-severity finding because it permits direct remote SSH authentication as the root account.

The recommended configuration is generally:
PermitRootLogin no
However, changes should be validated against the organization's access model before deployment. Administrators should ensure an alternative administrative account and appropriate privilege escalation mechanism are available before disabling root SSH access.

2. /etc/passwd Permissions
The tool verifies permissions on: /etc/passwd
The expected mode in the current implementation is: 0644

***Example:***

[LOW] Permissions: /etc/passwd
Status: PASS
Current: 0o644
Expected: 0o644

3. /etc/shadow Permissions
The tool also checks: /etc/shadow 
The current expected permission mode is: 0640

***Example:***

[LOW] Permissions: /etc/shadow
Status: PASS
Current: 0o640
Expected: 0o640

File ownership and group membership should also be considered when developing a more comprehensive hardening policy.

4. Firewall Availability
The audit checks whether common Linux firewall frameworks are available:
nftables
iptables

***Example:***

========== Firewall ===========
nftables: Available
iptables: Available

Important: Detecting that a firewall utility is installed does not prove that the host is protected by an effective firewall policy. Future versions should inspect the active ruleset and firewall state.

5. Service Auditing
The tool detects container environments.

When executed inside a container, it reports:
Container environment detected.
systemd service auditing skipped.
This prevents the tool from incorrectly assuming that systemd is available inside a container.

A future implementation could provide separate auditing logic for:
Docker containers
Podman containers
Kubernetes workloads
systemd-based hosts
Minimal Linux environments

6. Rootkit Detection
Rootkit detection is currently not implemented.

The tool intentionally reports:

[INFO] Rootkit Detection
Status: NOT_IMPLEMENTED
Current: No rootkit detection engine configured
Expected: Dedicated rootkit detection capability

This is preferable to presenting an unsupported rootkit check as a completed security control.
Future versions could integrate an approved host-based security or rootkit detection mechanism.

```

***Severity Model*** </br>

Findings are classified using the following severity levels:

| Severity | Meaning |
|----------|---------|
| 🔴 Critical | Potentially severe security exposure requiring immediate investigation |
| 🟠 High | Significant security weakness that should be addressed promptly |
| 🟡 Medium | Security issue requiring planned remediation |
| 🔵 Low | Lower-impact configuration or permission issue |
| ⚪ Info | Informational finding or functionality requiring further implementation |

Severity is assigned by the application's current audit logic and should not be interpreted as a formal CVSS score.

***Audit Result Interpretation***
```text
The current sample execution reports: Category	Result
Total Findings	4
Passed	2
Failed	1
Critical	0
High	1
Medium	0
Low	2
Info	1

The most significant finding in this sample is:

[HIGH] SSH Root Login
Status: FAIL
Current: PermitRootLogin yes
Expected: PermitRootLogin no

The /etc/passwd and /etc/shadow permission checks pass according to the tool's configured expectations.
The rootkit check is informational because the detection capability has not yet been implemented.

```

***Recommended Remediation:***
</br>
For the sample SSH finding, first verify that a separate administrative account can successfully authenticate and obtain required privileges.
Then review the SSH server configuration:
</br>
     
    ''' sudo grep -i '^PermitRootLogin' /etc/ssh/sshd_config
</br>
If organizational policy permits disabling direct root SSH access, configure: PermitRootLogin no
</br>
Validate the SSH configuration before restarting the service:
</br>
  
    ''' sudo sshd -t
</br>
Then reload the SSH service using the appropriate mechanism for the distribution.
</br>
Operational warning: </br>
Keep an existing administrative session open while changing SSH configuration. Incorrect SSH configuration can lock administrators out of the system. </br> </br>

***Limitations:*** </br>
```text
This project currently performs a relatively small set of security checks.

It does not currently provide comprehensive auditing for:
Kernel security configuration
Linux capabilities
SUID/SGID binaries
World-writable files
Cron jobs
Systemd service hardening
User account security
Password policies
SSH cryptographic configuration
Firewall rule effectiveness
Network exposure
Listening ports
SELinux/AppArmor configuration
File integrity monitoring
Rootkit detection
Package vulnerability scanning
Container image vulnerabilities
CIS benchmark compliance

```

***CVE assessment*** </br>
Therefore, a clean result from this tool should not be interpreted as proof that a Linux host is secure.
</br> </br>

***Roadmap***

```text
Planned improvements may include:
 SSH configuration hardening
 SSH cryptographic algorithm auditing
 Password policy checks
 User and group auditing
 SUID/SGID binary detection
 World-writable file detection
 Listening-port enumeration
 Firewall ruleset auditing
 Kernel parameter auditing
 SELinux/AppArmor checks
 Systemd service auditing
 Cron and scheduled-task auditing
 Package security checks
 Rootkit detection integration
 JSON output
 CSV reporting
 HTML security reports
 Configurable security baselines
 CIS benchmark mapping
 Unit and integration tests
 CI/CD security testing

 ```

***Security Design Principles:***
</br>The project follows several important security principles:

***Fail visibly:***
</br> Security checks should clearly report failures instead of silently ignoring insecure configurations.

***Don't claim unsupported detection:***
</br>A security control that has not been implemented should be reported as such rather than generating a misleading PASS result.

***Minimize assumptions:***
</br>Containerized environments and different Linux distributions can behave differently. The auditor should detect environmental limitations before performing platform-specific checks.</br></br>

***Provide actionable findings:***
</br> </br>
Each failed check should ideally provide:
</br>Current state
</br>Expected state
</br>Severity
</br>Recommendation
</br>
****This makes audit output useful for both administrators and security teams.****
</br> </br>
```text
***Responsible Use:****
This tool is intended for: 
System administrators
Linux engineers 
DevOps engineers 
Security engineers 
Blue teams 
Security students 
Authorized infrastructure assessments 
Only run security audits against systems for which you have explicit authorization.

```
Do not use this project to access, modify, disrupt, or assess third-party systems without permission.
 </br> </br>
**Contributing...**
</br> 
**Contributions are welcome.**




