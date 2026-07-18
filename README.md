<h1>Project: Harden Windows OS — CIS Benchmark Implementation on Windows Server 2019</h1></br>
<p> <h3>Description:</h3>
  This project demonstrates hardening a Windows Server 2019 VM against the CIS (Center for Internet Security) Benchmark controls. It covers building the test environment, applying local security policy and Group Policy settings across multiple CIS control categories (account policies, interactive logon, network security, UAC, firewall, auditing, and device/driver restrictions), and validating specific settings through registry inspection and documented audit/remediation procedures.
</p>
<p><h4>1. Creating the VM </h4>
Building a virtual machine ("Win2019CIS") in Oracle VirtualBox running Windows Server 2019 Standard Evaluation, using a downloaded ISO. This creates an isolated test environment to safely apply and test hardening settings without touching a production system.
  <img src="https://i.imgur.com/CHIjDSQ.jpeg">
</p>

<p> <h4>2. Password and account lockout policies (CIS 1.1.1–1.1.6, 1.2.1–1.2.3)

</h4>
   Configuring Local Security Policy settings for password strength and account protection: minimum password age/length, password history, complexity requirements, and account lockout threshold/duration — all standard CIS controls to resist brute-force and credential attacks.
   <img src="https://i.imgur.com/41oK0vz.jpeg"> <br>
    <img src="https://i.imgur.com/oq7N2NO.jpeg">
</p>

<p>
<h4>
  Interactive logon settings (CIS 2.3.7.1–2.3.7.5) 
</h4>
Hardening the login screen behavior: requiring CTRL+ALT+DEL before login, hiding the last signed-in username (prevents leaking valid usernames to shoulder-surfers), setting a 900-second inactivity lockout, and configuring a legal warning banner ("PROPER AUTHORIZATION REQUIRED!") that displays before logon.
 <img src="https://i.imgur.com/fVjdy39.jpeg">
  <img src="https://i.imgur.com/WkAsZAM.jpeg">
</p>


<p>
<h4>
  Network and UAC settings (CIS 2.3.11.2, 2.3.17.2) 
</h4>
 Disabling "LocalSystem NULL session fallback" (prevents anonymous/unauthenticated access to system resources) and setting User Account Control to "Prompt for consent on the secure desktop" (ensures elevation prompts can't be spoofed by malware).
 <img src="https://i.imgur.com/c3AhH9A.jpeg">
<img src="https://i.imgur.com/Co04VpT.jpeg">
</p>

<p>
<h4>
 Firewall configuration (CIS 9.1.1) 
</h4>
 Verifying Windows Defender Firewall is enabled for the Domain Profile, blocking inbound connections by default while allowing outbound — a baseline firewall posture.
 <img src="https://i.imgur.com/ktJlefT.jpeg">
<img src="https://i.imgur.com/Co04VpT.jpeg"> 
</p>
<p>
<h4>
 Audit policy (CIS 17.8.1)
</h4>
  Enabling "Audit Sensitive Privilege Use" for both success and failure events, ensuring the use of powerful privileges (like taking ownership of files) gets logged for security monitoring.
 <img src="https://i.imgur.com/KCaiLPE.jpeg">
</p>

<p>
<h4>
Device/driver restrictions (CIS 18.8.14.1, 18.8.22.1.1)
</h4>
 Enabling the Boot-Start Driver Initialization Policy restricted to "Good, unknown and bad but critical" drivers (blocks known-malicious boot drivers), and disabling downloading of print drivers over HTTP (reduces attack surface from untrusted print driver sources).
 <img src="https://i.imgur.com/xdBI7Kb.jpeg">
 <img src="https://i.imgur.com/uWKBGSh.jpeg">
</p>

<p>
<h4>
Analyzing CIS control 2.2.2
</h4>
("Access this computer from the network" set to Administrators, Authenticated Users, ENTERPRISE DOMAIN CONTROLLERS) and correctly identifying that this setting is inapplicable to the test VM because it isn't a domain controller and isn't joined to any domain — so the "ENTERPRISE DOMAIN CONTROLLERS" group has no meaning in this context.
</p>

<p>
<h4>
 Investigating CAD (Ctrl+Alt+Del) behavior via registry
</h4>
Digging into the registry (HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System) to confirm why CTRL+ALT+DEL is currently required: the disablecad value is set to 0, meaning CAD is not disabled — i.e., it's enabled/required. Cross-referencing this against the official CIS audit/remediation documentation, which specifies the exact registry key and GPO path controlling this setting, and its differing default values across Windows Server versions (Disabled by default on 2008 R2 and older, Enabled by default on 2012 and newer). Also documenting the equivalent for the UAC elevation prompt setting. (ConsentPromptBehaviorAdmin = 2, corresponding to "Prompt for consent on the secure desktop"), again matched against its official audit/remediation reference.
<img src="https://i.imgur.com/JTBY43K.jpeg">
</p>

<p>
<h4>
Verifying the logon banner after reboot</h4>
Rebooting the hardened VM and confirming that upon login, the configured legal warning message actually appears as expected — providing a screenshot as proof the interactive logon message settings (2.3.7.4–2.3.7.5) took effect correctly.

</p>


<h1>Closing Remarks
</h1> </br>
<p>This project applied CIS Benchmark hardening controls to a Windows Server 2019 environment, moving from a stock VM build to a system with verified, defense-in-depth security configurations.
Key accomplishments:

Built an isolated Windows Server 2019 VM to safely test hardening changes
Applied CIS-aligned account, password, and lockout policies to resist credential attacks
Hardened interactive logon behavior, including a legal warning banner and inactivity timeout
Locked down network security and UAC elevation behavior to reduce attack surface
Enabled firewall protections and sensitive privilege auditing for visibility and defense
Restricted risky legacy behaviors (unsigned boot drivers, HTTP print driver downloads)
Correctly identified a CIS control that didn't apply to this environment and explained why (no domain/domain controllers present)
Verified settings at the registry level, cross-referencing official CIS audit/remediation language
Validated the hardening worked end-to-end by rebooting and confirming the logon banner appeared

Takeaway:
Not every CIS control applies uniformly — context matters (e.g., domain-specific settings on a non-domain-joined machine). Verifying hardening at the registry level, rather than just the GUI, builds a much stronger understanding of what a setting is actually doing under the hood, and confirming behavior post-reboot is essential to prove a control actually took effect.
Want this compiled into one document or slide deck alongside the earlier hybrid identity project?
</p>














