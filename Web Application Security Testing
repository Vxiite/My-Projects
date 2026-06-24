## OWASP Juice Shop Security Assessment Lab

### Project Overview

This laboratory project focused on assessing the security posture of the intentionally vulnerable **OWASP Juice Shop** web application. The assessment was conducted from a controlled testing environment using **Burp Suite Community Edition** and standard web application security testing methodologies.

The primary objectives of this lab were:

1. Identifying authentication and authorization weaknesses.
2. Enumerating exposed API endpoints and application functionality.
3. Demonstrating exploitation of common web application vulnerabilities.
4. Evaluating the impact of insecure configurations and sensitive information exposure.
5. Documenting findings according to severity and business risk.

---

## Target Environment

### Application Information

* **Target Application:** OWASP Juice Shop
* **Target Address:** `192.168.2.10:3000`
* **Testing Platform:** Kali Linux
* **Primary Assessment Tool:** Burp Suite Community Edition

### Assessment Scope

The assessment focused on:

* Authentication mechanisms
* Authorization controls
* API endpoint security
* Information disclosure vulnerabilities
* Administrative functionality exposure

---

## Step 1: Reconnaissance & Environment Validation

### 1. Connectivity Verification

Network connectivity to the target application was confirmed prior to testing.

```bash
ping 192.168.2.10
```

### 2. Interface Validation

The attack workstation network configuration was verified using:

```bash
ip a
```

### 3. HTTP Traffic Interception

Burp Suite Community Edition was configured as an intercepting proxy to inspect requests and responses between the client and the target application.

Initial reconnaissance focused on identifying:

* Available API endpoints
* Authentication requests
* Application routing behavior
* Administrative functionality

---

## Step 2: API Enumeration & Product Discovery

### Product Endpoint Testing

Burp Intruder was utilized to perform controlled enumeration against the product API endpoint:

```http
/api/Products/
```

Multiple product identifiers were supplied through automated requests to determine how the application handled object references.

### Results

The application returned valid product records for multiple identifiers, confirming:

* Predictable API object references
* Accessible product enumeration
* API responsiveness to automated testing

This activity provided a clearer understanding of the application's backend structure and available resources.

---

## Step 3: Authentication Testing & SQL Injection Exploitation

### Initial Authentication Assessment

Authentication requests were directed to:

```http
/rest/user/login
```

Standard login attempts resulted in:

```http
401 Unauthorized
```

indicating that authentication validation was functioning under normal conditions.

### SQL Injection Discovery

Inspection of application behavior suggested inadequate sanitization of user-supplied input.

The following SQL Injection payload was tested:

```sql
' OR 1=1--
```

### Exploitation Outcome

The payload successfully bypassed password validation and returned an administrative JSON Web Token (JWT).

This demonstrated a critical SQL Injection vulnerability capable of compromising authentication controls and administrative accounts.

---

## Step 4: Privilege Escalation & Administrative Access

### Session Persistence

To maintain authenticated administrative access during testing, a Burp Suite Match and Replace rule was configured to automatically insert the administrative JWT into outbound requests.

### Validation

Administrative access was verified through successful completion of the application's internal administrative challenge and access to previously restricted resources.

### Security Impact

Successful exploitation resulted in:

* Administrative account compromise
* Elevated application privileges
* Persistent authenticated access
* Unauthorized access to privileged functionality

---

## Step 5: Administrative Data Extraction & Enumeration

### 1. User Enumeration

The following endpoint was assessed:

```http
/api/Users/
```

Results included a complete listing of registered application users and associated account information.

### 2. Configuration Disclosure

The following endpoint was evaluated:

```http
/rest/admin/application-configuration
```

The response disclosed sensitive configuration details, including:

* Internal proxy URIs
* OAuth redirect configurations
* Environment-specific settings
* Administrative application parameters

### 3. Application Logic Disclosure

The CAPTCHA endpoint was examined:

```http
/rest/captcha/
```

The response exposed information relating to internal application logic used to process security controls and validation workflows.

---

## Step 6: Risk Analysis & Findings Summary

| Vulnerability | Severity | Impact |
|--------------|----------|----------|
| SQL Injection | Critical | Authentication bypass and administrative account compromise |
| Broken Access Control | High | Unauthorized access to user and administrative resources |
| Sensitive Data Exposure | Medium | Disclosure of internal application configuration details |
| Information Disclosure | Low | Exposure of application logic and implementation details |

---

## Key Technical Skills Demonstrated

### Web Application Security Testing

- Conducted application-layer security assessments using Burp Suite.
- Performed endpoint discovery and API enumeration.
- Intercepted and analyzed HTTP requests and responses.

### Vulnerability Assessment & Exploitation

- Identified SQL Injection vulnerabilities.
- Demonstrated authentication bypass techniques.
- Validated privilege escalation paths through controlled exploitation.

### Access Control Analysis

- Evaluated authentication and authorization mechanisms.
- Identified broken access control weaknesses.
- Assessed administrative functionality exposure.

### Security Analysis & Reporting

- Documented findings according to severity and business impact.
- Assessed risks associated with insecure application design.
- Produced structured security assessment documentation suitable for technical review.
````
