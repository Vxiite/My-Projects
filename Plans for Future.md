## Plans for Future Expansion and other Projects

To continuously develop this lab environment and simulate deeper enterprise security workflows, the next phases of this project will focus on the following implementations:

### 1. Architectural Modeling via Cisco Packet Tracer
* **Objective:** Design and document a production-ready hardware replica of this network topology using Cisco Packet Tracer.
* **Implementation Details:** * Map out the virtualized architecture into physical layer components (e.g., dedicated Cisco ASA hardware firewalls, Catalyst multilayer switches, and endpoint nodes).
  * Enforce matching Access Control Lists (ACLs) on the router interfaces to mirror the stateful rulesets currently running on the pfSense security appliance.
  * Provide a clear visual network layout diagram directly inside the project repository to enhance architectural documentation and deployment design.

### 2. Threat Simulation: Active Directory Brute-Forcing & Password Spraying
* **Objective:** Conduct targeted brute-force and password-spraying campaigns from the `hacker-net` segment to validate active security baselines.
* **Implementation Details:**
  * Utilize tools like `Hydra` or `CrackMapExec` to target the `robcorp.local` domain controller endpoints.
  * Validate that the custom **Fine-Grained Password Policy (FGPP)** successfully prevents credential exhaustion through account lockout mechanisms.
  * Audit and capture Windows Security Event ID `4625` (An account failed to log on) to ensure visibility within administrative event logs.

### 3. Web Application Security Assessment (Separate Project)
* **Objective:** Evaluate intentionally vulnerable web applications to identify, exploit, and document critical application-layer vulnerabilities.
* **Implementation Details:**
  * Deploy an instance of **OWASP Juice Shop** within the network architecture to simulate a modern, production web application environment.
  * Intercept, modify, and analyze HTTP traffic using **Burp Suite Community Edition** to map the application attack surface.
  * Actively investigate and exploit common application weaknesses, focusing on authentication flaws (e.g., broken object-level authentication), input validation issues (e.g., SQL Injection, XSS), and other top web application attack vectors.
  * Document all technical findings alongside standardized remediation strategies and secure coding recommendations to demonstrate full-cycle security testing capabilities.
