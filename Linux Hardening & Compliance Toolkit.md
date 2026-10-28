# 🔐 Linux Hardening & Compliance Toolkit

**A lightweight Python-based Linux security auditing tool for identifying configuration weaknesses, validating critical file permissions, detecting firewall utilities, and generating actionable security findings.**

The Linux Hardening & Compliance Toolkit helps system administrators, Linux engineers, cybersecurity professionals, and blue-team practitioners assess selected Linux security controls and identify potential hardening opportunities.

The toolkit focuses on SSH root login configuration, critical system file permissions, firewall utility availability, container environment detection, and transparent reporting of unimplemented security capabilities.

> **Security Notice:** This project is a lightweight security auditing utility, not a comprehensive hardening framework, vulnerability scanner, or compliance certification tool. Always validate findings against your organization's security baseline before applying remediation.

## 📌 Table of Contents

- Project Overview
- Key Features
- Project Structure
- Requirements
- Installation
- Usage
- Sample Audit Output
- Security Checks Explained
- Severity Classification
- Understanding Audit Results
- Remediation Guidance
- Security Design Principles
- Current Limitations
- Roadmap
- Intended Audience
- Responsible Use
- Contributing
- Disclaimer

---

## 🎯 Project Overview

Linux systems power enterprise infrastructure, cloud platforms, containerized applications, and critical production services. Misconfigured remote access, insecure file permissions, and inadequate security controls can increase the attack surface and expose systems to unauthorized access.

The Linux Hardening & Compliance Toolkit provides a practical starting point for identifying selected configuration weaknesses through lightweight, Python-based security auditing.

### Project Objectives

- 🔍 **Identify misconfigurations:** Assess selected SSH and filesystem security settings.
- 🛡️ **Evaluate basic security controls:** Detect the availability of common Linux firewall utilities.
- 🐧 **Recognize execution environments:** Detect containerized environments and skip inappropriate systemd service auditing.
- 📊 **Deliver actionable findings:** Report observed values, expected values, severity, status, and remediation guidance.
- 🧩 **Support future development:** Establish a foundation for expanded Linux hardening and compliance assessment capabilities.

The project emphasizes transparency, practical remediation, and honest reporting of implemented and unimplemented controls.

## ✨ Key Features

### 🔐 SSH Security Auditing

Evaluates SSH server configuration for direct root login.

- Identifies an explicitly configured `PermitRootLogin yes`.
- Compares the observed setting against the configured expectation.
- Reports findings with severity classifications and remediation recommendations.

**Security objective:** Reduce unnecessary direct privileged remote access.

**Recommended baseline:** `PermitRootLogin no`, where appropriate for the organization's administrative access model.

### 📁 Critical File Permission Checks

Validates the configured permission expectations for security-sensitive Linux files.

| File | Expected Mode | Security Purpose |
| --- | --- | --- |
| `/etc/passwd` | `0644` | Protect account database integrity |
| `/etc/shadow` | `0640` | Restrict access to password-hash information |

The toolkit compares observed permission modes against its configured expectations.

> **Note:** Expected permissions can vary by Linux distribution and organizational baseline. Ownership, group membership, access control lists, and other access controls should also be evaluated.

### 🔥 Firewall Utility Detection

Detects the availability of common Linux firewall utilities:

- `nftables`
- `iptables`

This check establishes whether the utilities are available in the target environment.

**Important:** Utility availability does not prove that a firewall is enabled, that its ruleset is active, or that effective traffic filtering is enforced.

### 🐧 Container Environment Detection

Detects containerized execution environments where supported by the implementation.

- Identifies a container environment.
- Reports the detected execution context.
- Skips systemd service auditing when a container is detected.

This behavior helps avoid inappropriate assumptions about traditional Linux host environments.

### 🛡️ Rootkit Detection Status

Reports whether rootkit detection functionality is implemented.

The current version does not include an actual rootkit detection engine. Instead, it reports the capability as `NOT_IMPLEMENTED`.

This prevents an unsupported security control from being incorrectly presented as a successful check.

### 📊 Security Audit Summary

Provides a consolidated view of the audit results, including:

- Total findings.
- Passed and failed checks.
- Severity distribution.
- Individual security findings.
- Current and expected values.
- Remediation recommendations.
- Informational statuses for unsupported or unimplemented capabilities.

---

## 📂 Project Structure

text

```
Python-Automation-Tools/
└── Linux-Hardening-and-Audit-Tool/
    ├── main.py
    └── README.md
```

| File | Description |
| --- | --- |
| `main.py` | Main entry point for Linux security auditing |
| `README.md` | Project documentation and usage instructions |

This structure reflects the currently documented implementation. Additional modules, tests, configuration files, and reporting components can be introduced as the project evolves.

## ⚙️ Requirements

| Requirement | Description |
| --- | --- |
| Operating system | Linux |
| Python | Python 3.x |
| Permissions | Read access to the configuration files being audited |
| Firewall utilities | Optional; detected when available |
| systemd | Environment-dependent |

Some checks may require elevated privileges depending on the target system and file permissions.

**Recommendation:** Begin with an unprivileged execution and use elevated privileges only when necessary. Review the source code before executing it as root.

## 🚀 Installation

### 1\. Clone the Repository

Replace the example URL with your actual GitHub repository.

bash

```
git clone https://github.com/SRCybersecurity/Python-Automation-Tools.git
```

### 2\. Navigate to the Project Directory

bash

```
cd Python-Automation-Tools/Linux-Hardening-and-Audit-Tool
```

Adjust the directory path if your repository uses a different structure.

### 3\. Verify Python

bash

```
python3 --version
```

### 4\. Run the Security Audit

bash

```
python3 main.py
```

If permission restrictions prevent access to specific security configuration files, review the errors and determine whether elevated privileges are necessary.

bash

```
sudo python3 main.py
```

> **Operational caution:** Execute the toolkit only on systems you own or are explicitly authorized to assess.

## ▶️ Usage

Run the tool from the project directory:

bash

```
python3 main.py
```

The tool performs its implemented checks and prints the results to the terminal.

Depending on the environment, the report may include:

- SSH root login configuration.
- `/etc/passwd` permission validation.
- `/etc/shadow` permission validation.
- `nftables` and `iptables` availability.
- Container environment detection.
- Rootkit detection implementation status.
- An aggregate security audit summary.

The exact findings depend on the target environment and the current implementation.

## 🖥️ Sample Audit Output

The following output illustrates the documented reporting format. Actual output may differ depending on the target system.

text

```
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
Recommendation: Integrate an approved rootkit detection
mechanism before relying on this check.

===============================
Audit completed
===============================
```

**Reporting note:** The example above contains an inconsistency: the severity counts total three, while the summary states four findings. The implementation and sample output should be reconciled before publication.

## 🔍 Security Checks Explained

### 1\. SSH Root Login Configuration

**Configuration under assessment:** `PermitRootLogin`

SSH provides remote administrative access to Linux systems. Permitting direct root authentication can increase the impact of compromised credentials and expose a privileged account to remote authentication attempts.

An explicitly configured value such as:

text

```
PermitRootLogin yes
```

should be reviewed against the organization's security policy.

A commonly recommended baseline is:

text

```
PermitRootLogin no
```

The effective configuration may depend on included configuration files, directive ordering, and other OpenSSH settings. A simple text-based check may not capture every effective configuration scenario.

**Validate the effective configuration:**

bash

```
sudo sshd -T | grep -i '^permitrootlogin'
```

Before changing this setting, verify that an alternative administrative account can authenticate and obtain the privileges required for system administration.

### 2\. `/etc/passwd` Permissions

The `/etc/passwd` file stores account metadata, including usernames, user IDs, group IDs, home directories, and login shells.

The documented expected permission mode is:

text

```
0644
```

Example finding:

text

```
[LOW] Permissions: /etc/passwd
Status: PASS
Current: 0o644
Expected: 0o644
```

A passing result indicates that the observed permission mode matches the configured expectation. It does not establish that the file contents, ownership, or other access controls are secure.

### 3\. `/etc/shadow` Permissions

The `/etc/shadow` file stores password-hash information and password-aging metadata on systems using the traditional shadow password mechanism.

The documented expected permission mode is:

text

```
0640
```

Example finding:

text

```
[LOW] Permissions: /etc/shadow
Status: PASS
Current: 0o640
Expected: 0o640
```

The expected mode and associated ownership must be validated against the target distribution's security policy.

**Future enhancement:** Extend validation to include ownership, group membership, ACLs, symbolic-link handling, and unexpected file replacements.

### 4\. Firewall Utility Availability

The toolkit checks whether these utilities are available:

- `nftables`
- `iptables`

Example:

text

```
========== Firewall ===========
nftables: Available
iptables: Available
```

The results indicate utility availability only. They do not establish:

- Whether the firewall is active.
- Whether the intended ruleset is loaded.
- Whether inbound and outbound traffic policies are appropriate.
- Whether IPv4 and IPv6 policies are consistent.
- Whether another firewall management service enforces the policy.

**Future enhancement:** Inspect the active ruleset and relevant service state, then evaluate the configuration against a defined firewall baseline.

### 5\. Container and Service Detection

Some containers do not use systemd as their init system. Performing host-oriented service checks inside such environments can produce misleading results.

When a container is detected, the documented behavior is:

text

```
Container environment detected.
systemd service auditing skipped.
```

This is an environment-aware limitation, not proof that the container is secure.

Potential future enhancements include environment-specific checks for:

- Docker containers.
- Podman containers.
- Kubernetes workloads.
- systemd-based Linux hosts.
- Minimal Linux environments.

Container detection is not infallible and may be limited by the process's visibility into the host or runtime.

### 6\. Rootkit Detection Status

Rootkits may attempt to conceal processes, files, kernel modules, network activity, or other indicators of compromise.

The current toolkit does not implement rootkit detection and reports:

text

```
[INFO] Rootkit Detection
Status: NOT_IMPLEMENTED
Current: No rootkit detection engine configured
Expected: Dedicated rootkit detection capability
```

This is an informational finding because the capability is absent. It does not indicate that the system has been verified free of rootkits.

Potential future integrations include approved host-based detection tools, integrity verification mechanisms, and established incident-response workflows.

No single detection mechanism can guarantee that a host is free from compromise.

## 🚦 Severity Classification

The toolkit uses severity labels to communicate the relative importance of its findings.

| Severity | Meaning | Suggested Response |
| --- | --- | --- |
| 🔴 Critical | Potentially severe security exposure | Investigate immediately and contain where warranted |
| 🟠 High | Significant security weakness | Prioritize remediation |
| 🟡 Medium | Security issue requiring planned remediation | Assess impact and establish a remediation timeline |
| 🔵 Low | Lower-impact configuration or permission issue | Correct according to the applicable baseline |
| ⚪ Info | Informational status, limitation, or unimplemented capability | Review and determine whether additional controls are needed |

Severity is assigned by the application's current audit logic. It is not a formal CVSS score, verified exploitability assessment, or comprehensive risk rating.

## 📊 Understanding Audit Results

Consider the illustrative sample:

| Metric | Example Result |
| --- | --- |
| Total findings | 4 |
| Passed checks | 2 |
| Failed checks | 1 |
| Critical | 0 |
| High | 1 |
| Medium | 0 |
| Low | 2 |
| Informational | 1 |

The severity counts sum to three, while the reported total and status counts imply four findings. This discrepancy should be corrected in the program's reporting logic or sample output.

The intended interpretation is:

- **SSH root login:** A high-severity configuration finding requiring review.
- **`/etc/passwd`****:** The observed mode matches the configured expectation.
- **`/etc/shadow`****:** The observed mode matches the configured expectation.
- **Rootkit detection:** The capability is not implemented, so no rootkit detection conclusion can be drawn.

A clean audit result must not be interpreted as proof that the host is secure. It means only that the implemented checks produced the reported results.

## 🛠️ Remediation Guidance

### Addressing an SSH Root Login Finding

If the audit identifies:

text

```
[HIGH] SSH Root Login
Status: FAIL
Current: PermitRootLogin yes
Expected: PermitRootLogin no
```

Follow a controlled change process.

**Step 1: Review the effective configuration**

bash

```
sudo sshd -T | grep -i '^permitrootlogin'
```

**Step 2: Review relevant configuration files**

bash

```
sudo grep -Rni '^[[:space:]]*PermitRootLogin' \
    /etc/ssh/sshd_config /etc/ssh/sshd_config.d/ 2>/dev/null
```

This searches the specified locations for matching directives. It does not independently determine which value ultimately takes effect.

**Step 3: Validate administrative access**

Confirm that an alternative administrative account can authenticate and obtain the required privileges before disabling direct root SSH access.

**Step 4: Apply the approved configuration**

If organizational policy requires disabling direct root SSH login, configure the effective SSH policy accordingly:

text

```
PermitRootLogin no
```

**Step 5: Validate the SSH configuration**

bash

```
sudo sshd -t
```

Proceed only if validation succeeds.

**Step 6: Reload the SSH service safely**

Use the appropriate service name and reload mechanism for the Linux distribution. Keep an existing administrative session open and verify a new connection before ending the existing session.

> **Operational warning:** Incorrect SSH configuration can lock administrators out of remote systems. Test changes in a controlled environment and maintain an approved recovery path.

## 🧱 Security Design Principles

### 1\. Fail Visibly

Checks should report inaccessible files, parsing errors, unsupported environments, and failed validations rather than silently treating them as successful.

### 2\. Never Claim Unsupported Detection

Unimplemented controls should be reported as `NOT_IMPLEMENTED` or an equivalent explicit status—not as a passing security check.

### 3\. Minimize Environmental Assumptions

Linux distributions, container runtimes, service managers, and filesystem policies differ. Checks should identify relevant limitations and avoid unsupported assumptions about the target environment.

### 4\. Provide Actionable Findings

Where applicable, each finding should include:

- Check name.
- Observed state.
- Expected state.
- Pass, fail, or implementation status.
- Severity.
- Remediation recommendation.
- Relevant limitations.

### 5\. Prefer Safe, Read-Only Auditing

Auditing should not silently modify system configuration. Remediation should be reviewed and applied through a controlled administrative process.

### 6\. Make Results Reproducible

Future versions should provide deterministic output, explicit error handling, consistent severity assignment, and tests for expected and unexpected system configurations.

### 7\. Avoid False Assurance

A successful check must describe exactly what was verified. The presence of a utility, a matching permission mode, or an unimplemented detection placeholder must never be presented as evidence of comprehensive host security.

## ⚠️ Current Limitations

The current project implements a limited set of Linux security checks.

The following capabilities are potential future enhancements and should not be considered implemented unless supported by the actual source code.

| Security Domain | Potential Enhancement |
| --- | --- |
| SSH security | Effective configuration validation and cryptographic algorithm auditing |
| Identity and access management | User, group, account, and password-policy auditing |
| Filesystem security | SUID/SGID discovery, world-writable file detection, and ownership validation |
| Linux kernel | Kernel parameter and security configuration auditing |
| Service hardening | systemd service configuration and privilege assessment |
| Scheduled execution | Cron jobs and scheduled-task auditing |
| Network security | Listening-port enumeration and network exposure checks |
| Firewall security | Active ruleset inspection and policy validation |
| Mandatory access control | SELinux and AppArmor checks |
| Integrity monitoring | Critical file integrity validation |
| Malware detection | Integration with approved host-based or rootkit detection tools |
| Package security | Package inventory and vulnerability assessment |
| Container security | Container configuration and image vulnerability assessment |
| Compliance | Mapping checks to approved CIS benchmarks or organizational baselines |
| Reporting | JSON, CSV, and HTML report generation |
| Quality assurance | Automated unit, integration, and regression testing |

### CVE Assessment

The current toolkit is not a CVE scanner and does not establish whether installed packages are affected by known vulnerabilities.

A future package-security module could integrate trusted vulnerability data and distribution-specific package metadata.

**Important:** Passing all currently implemented checks does not establish CIS benchmark compliance, eliminate CVE exposure, or prove that a Linux host is free from compromise.

## 🗺️ Roadmap

The following roadmap identifies potential development priorities. These are planned enhancements, not claims about existing functionality.

### Phase 1 — Core Audit Reliability

- [ ]Improve SSH configuration parsing and effective-value validation.
- [ ]Validate file permissions, ownership, and group membership.
- [ ]Improve error handling and unsupported-state reporting.
- [ ]Reconcile finding totals, severity counts, and status counts.
- [ ]Add unit tests for existing security checks.

### Phase 2 — Linux Hardening Coverage

- [ ]Add user and group security auditing.
- [ ]Assess password policies and account configuration.
- [ ]Detect SUID/SGID binaries and world-writable files.
- [ ]Audit kernel security parameters.
- [ ]Inspect listening ports and firewall rules.
- [ ]Add SELinux and AppArmor checks.
- [ ]Expand systemd and scheduled-task auditing.

### Phase 3 — Reporting and Automation

- [ ]Introduce structured JSON output.
- [ ]Add CSV export and HTML security reports.
- [ ]Support configurable security baselines.
- [ ]Add machine-readable exit codes.
- [ ]Enable integration with CI/CD and scheduled audit workflows.

### Phase 4 — Compliance and Advanced Detection

- [ ]Map applicable checks to selected CIS benchmark recommendations.
- [ ]Add distribution-aware compliance profiles.
- [ ]Integrate approved package vulnerability assessment.
- [ ]Evaluate host-based rootkit detection integrations.
- [ ]Expand container and cloud workload security checks.
- [ ]Add regression testing and automated security quality gates.

Roadmap items should be marked complete only after implementation, testing, and documentation.

## 👥 Intended Audience

This project is designed for:

- **Linux System Administrators** — Reviewing baseline host configuration.
- **Cybersecurity Engineers** — Building foundational host-security assessment workflows.
- **Blue-Team Professionals** — Supporting defensive configuration reviews.
- **DevSecOps Engineers** — Exploring automated security checks within infrastructure workflows.
- **Cloud and Platform Engineers** — Assessing selected controls on Linux-based workloads.
- **Security Students** — Learning Linux hardening fundamentals and Python-based security automation.
- **Authorized Security Assessors** — Performing limited configuration assessments within an approved scope.

The toolkit complements rather than replaces established enterprise security controls, vulnerability management platforms, endpoint detection systems, or formal compliance assessments.

## 🤝 Contributing

Contributions are welcome from Linux engineers, cybersecurity practitioners, Python developers, and open-source contributors.

### Areas for Contribution

- Additional Linux security checks.
- Improved SSH and filesystem validation.
- Distribution-aware security baselines.
- Unit and integration tests.
- Structured output and reporting.
- Documentation improvements.
- Bug fixes and false-positive reduction.
- Secure coding and error-handling improvements.

### Contribution Workflow

1. Fork the repository.
2. Create a focused feature branch.
3. Implement the change with appropriate error handling.
4. Add tests covering normal, failure, and unsupported scenarios.
5. Verify that existing functionality remains intact.
6. Update documentation and identify relevant limitations.
7. Submit a pull request describing the security objective, implementation, test results, and potential operational impact.

### Security-Focused Contribution Guidelines

Contributors should ensure that new checks:

- Do not unexpectedly modify system configuration.
- Do not expose sensitive system information unnecessarily.
- Distinguish failed checks from unsupported or unimplemented checks.
- Avoid treating inaccessible files as successful checks.
- Document distribution-specific assumptions.
- Provide actionable and technically accurate remediation guidance.

For vulnerabilities discovered in the project itself, use the repository's designated private vulnerability-reporting mechanism if one is configured. Avoid publishing sensitive exploit details before maintainers have had an opportunity to assess the issue.

## 🔒 Responsible Use

This project is intended for authorized defensive security activities.

Appropriate use cases include:

- Auditing systems you own or administer.
- Conducting approved internal security assessments.
- Learning Linux hardening and security automation.
- Testing security controls in isolated lab environments.
- Supporting authorized infrastructure reviews.

Do not use this project to access, modify, disrupt, or assess third-party systems without explicit authorization.

Before executing the toolkit with elevated privileges, review the code, understand its behavior, and confirm that the assessment is within the approved scope.

## 📜 Disclaimer

This project is provided for educational, administrative, and defensive security purposes.

The toolkit performs only the checks implemented in its current version. It may not identify all security weaknesses, and results may be affected by distribution-specific configurations, permissions, environmental limitations, and implementation defects.

The project does not guarantee system security, regulatory compliance, vulnerability-free operation, or detection of malicious activity.

System owners and administrators remain responsible for validating findings, assessing operational risk, and approving remediation actions.

## ⭐ Support the Project

If you find this project useful:

- ⭐ Star the repository to support development.
- 🍴 Fork it and experiment in a controlled lab environment.
- 🐛 Report bugs and contribute reproducible test cases.
- 🔧 Submit improvements that strengthen security coverage and reliability.
- 📚 Share feedback that makes Linux security auditing more transparent and actionable.

**Build better security checks. Report honestly. Remediate responsibly.**
