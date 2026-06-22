## Plans for Future Expansion

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
