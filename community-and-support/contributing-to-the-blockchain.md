# Contributing to the Blockchain

## Bug Bounty Programme

Become a Zetrix security hero! We have a Bug Bounty Programme where you can help us find and fix security issues.

### **What is Bug Bounty Programme?**

**Finding Security Holes**

These initiatives aid in identifying security issues prior to malevolent individuals exploiting them, thereby safeguarding individuals’ finances and data from theft.

**Get Rewarded**

Zetrix offers rewards (from $ZETRIX 100 to $ZETRIX 10,000) for finding security problems in their system that could hurt their users or business.

### **Benefits of Zetrix Bug Bounty Programme**

Flex your coding skills, unearth hidden weaknesses, and help keep Zetrix strong!

1\) Financial Rewards

2\) Skill Development

3\) Recognition and Reputation

4\) Contribution to Security

5\) Legal Protection

6\) Learning Opportunities

### **How to submit report?** <a href="#submit-report" id="submit-report"></a>

Security researchers should email details of any suspected vulnerabilities affecting $ZETRIX or its users to [**disclosures@zetrix.com**](mailto:disclosures@zetrix.com), and include the following information in their report:

* A brief description of the issue and all instances or endpoints at which it is located.
* Attack scenario/exploitability, and the security impact of the bug.
* Screenshots and/or videos demonstrating the issue.
* Step-by-step instructions on how to reproduce the issue, including any exploit code.
* Operating system and/or version information, if relevant.
* If applicable, a log of all activity related to your discovery, including your IP address(es) and timestamped requests to aid us in validation and investigation.

**Disclaimer: In regards to the above, please note the following:**

* Do not upload screenshots, videos, or exploit code to a publicly accessible server/repository in preparation of your email.
* Do not zip or archive your files (just attach them directly to the email).
* Very low-quality reports such as those which only contain automated output will be rejected.

If necessary, you may use our PGP public key to encrypt your communication with us.\
Reports may be submitted anonymously. Alternatively, a security researcher may provide contact information as well as any preferred communication methods or times of day to communicate, as they see fit.

\
**Reward conditions and tiers**

Zetrix may offer monetary recognition for vulnerability reports that have a significant business impact on our users, products, or services. Rewards for qualifying findings range from $ZETRIX 100 to $ZETRIX 10,000 in appreciation for your help.

**Note: $ZETRIX is a Zetrix issued native currency**

Eligibility for monetary recognition is determined by calculating the internal severity of a finding against the potential impact to Zetrix and its userbase. We reserve the right, in our sole discretion, to determine vulnerability qualification for a monetary reward. The following rules apply if the issue is deemed to be valid and significant:

1. You must agree and adhere to the Programme Rules as stated previously.
2. You must be the first person to report the issue to us and provide a comprehensive report. We will review duplicate issues based on completeness of information provided, but priority lays with first reporter.
3. You must be available to supply additional information as needed by our team to reproduce and triage the issue.
4. The existence of a vulnerability in multiple applications will be factored into a recognition decision; duplicate reports will be closed without recognition.
5. We provide a reward at the time of fix based on $ZETRIX, Zetrix’s native currency.

### **General guidelines**

* Report any suspected or confirmed vulnerability you discovered immediately via [**disclosures@zetrix.com**](mailto:disclosures@www.zetrix.com).
* Please provide detailed reports with steps that can be replicated. The issue may be closed as Information or N/A if the report isn’t thorough enough to duplicate the problem.
* Multiple vulnerabilities caused by the same underlying issue will be regarded as one; the initial report will be triaged as the original, and all subsequent reports will be closed as duplicates.
* Social engineering (e.g. phishing, vishing, smishing) is prohibited.
* Do not intentionally view, store, modify, or destroy data that does not belong to you.
* Avoid perform denial of service attacks, or any attacks that have a reasonable chance of degrading Zetrix’s service or user experience.
* Make a good faith attempt to avoid data loss, privacy violations, and service interruptions or degradation. Only engage with accounts that you own or have specific authorization to interact with.
* Provide us with a reasonable amount of time to remediate vulnerabilities.

### **Scope of policy**

Zetrix may offer monetary recognition for vulnerability reports that have a significant business impact on our users, products, or services. Rewards for qualifying findings range from $ZETRIX 100 to $ZETRIX 10,000 in appreciation for your help.

* Zetrix-owned production web domains, such as: **\*.zetrix.com**

Any service which is not listed here are considered out-of-scope and are not authorized for testing. The scope of the Zetrix system and services covered by this policy will be periodically updated. If vulnerability discovered in third-party asset, the security researcher should report directly to the vendor in accordance with vendor disclosure policy (if any). If a security researcher aren’t sure whether a system is in scope or not, please contact us at [**disclosures@zetrix.com**](mailto:disclosures@zetrix.com).

### **Code of conduct**

By participating in Zetrix Vulnerability Disclosure Programme (VDP), security researchers agree to adhere with Zetrix Code of Conduct, listed as below:

* Report vulnerabilities immediately after discovery.
* Only contact Zetrix security team through approved channels and respect the request of Zetrix security team.
* Do not perform unsafe testing without prior authorization.
* Do not disclose report information, confidential information or personal data of our users without our consent. Respect our privacy.
* Do not violate any laws or regulations to conduct vulnerability research.
* Do not store, save, or generally abuse any sensitive data exposed by your research.
* Avoid disrupting our services in the course of your investigation.
* Extortion and blackmail is prohibited. Any attempt to obtain bounties, money or services by coercion may amount to a criminal offense.

### **Non-qualifying vulnerabilities**

To report vulnerability, security researcher are required to consider (1) attack scenario/exploitability and the (2) security impact of the bug. Any of the following actions could result in permanent exclusion from the disclosure programmed, as well as a criminal and/or legal investigation.

We do not tolerate any behaviors that may negatively damage other Zetrix users’ experiences on our system and services. We accept only manual or semi-manual tests. All findings coming from automated tools or scripts will be considered out of scope.

* Attacks designed or likely to degrade, deny, or adversely impact services or user experience (e.g., Denial of Service, Distributed Denial of Service, Brute Force, Password Spraying, Spam…).
* Attacks designed or likely to destroy, corrupt, make unreadable (or attempts therein) data or information that does not belong to you.
* Intentionally accessing data or information that does not belong to you beyond the minimum viable access necessary to demonstrate the vulnerability.
* Performing physical, social engineering, or electronic attacks against our personnel, offices, wireless networks, or property.
* Security issues in third-party applications, services, or dependencies that integrate within the products or infrastructure that do not have demonstrable proof of concept for the vulnerability (e.g., libraries, SAAS services).
* Security issues or vulnerabilities created or introduced by the reporter (e.g., modifying a library we rely on to include a vulnerability for the sole purpose of receiving a recognition).
* Attacks performed on any systems not explicitly mentioned as authorized and in-scope.
* Reports of missing “best practices” or other guidelines which do not indicate a security issue.
* Attacks related to email servers, email protocols, email security (e.g., SPF, DMARC, DKIM), or email spam.
* Missing cookie flags on non-sensitive cookies.
* Reports of insecure SSL/TLS ciphers (unless accompanied by working proof of concept).
* Reports of how you can learn whether a given client can authenticate to a product or service.
* Reports of mappings between code names and client names.
* Reports of simple IP or port scanning.
* Missing HTTP headers (e.g. lack of HSTS).
* Email security best practices or controls (e.g. SPF, DKIM, DMARC).
* Software or infrastructure bannering, fingerprinting, or reconnaissance with no proven vulnerability.
* Clickjacking or self-XSS reports.
* Reports of publicly resolvable or accessible DNS records for internal hosts or infrastructure.
* Domain-based phishing, typo squatting, punycodes, bitflips, or other techniques.
* Violating any laws or breaching any agreements (or any reports of the same).

### **Eligible findings**

The following classes of vulnerabilities are of particular interest to us, and are eligible for attribution upon review:

* Remote Code Execution (RCE)
* SQL injection
* XML External Entity Injection (XXE)
* Authorization bypass/escalation
* Sensitive information leaks
* Cross-site scripting (XSS)
* Cross-site request forgery (CSRF)
* Business logic Vulnerabilities (with Impact)

### **Response target**

Zetrix will make a best effort to meet the following response targets for security researcher participating in our programmed:

* Time to first response – 3 business days
* To the best of our ability, we will confirm the existence of the vulnerability to you and be as transparent as possible about what steps we are taking during the remediation process, including on issues or challenges that may delay resolution.

\*Zetrix calculates severity based on CVSS 3.0, business impact and environment.

### **Safe harbor**

Testing activities conducted in accordance with the Zetrix VDP programme regulation are protected by Safe Harbor, meaning we will not initiate legal action against you. However, if you violate the rules, Zetrix retains all other rights and remedies available to it at law, including the rights to seek legal action or law enforcement notice. Security researcher are expected, to comply with all laws applicable to you, and not to disrupt or compromise any data beyond what our VDP programs permit.

Thank you for helping us keep Zetrix’s user and data safe.
