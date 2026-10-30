# Microsoft Entra Conditional Access Architecture

## Project Overview

This project demonstrates the design, implementation, and validation of a layered Microsoft Entra Conditional Access architecture.

The environment was configured to protect privileged administrators, internal users, external B2B identities, risky sign-ins, and access from untrusted network locations while also reducing exposure to legacy authentication.

All policies were initially deployed in **Report-only mode** so their impact could be evaluated before enforcement.

---

## Business Problem

Organizations need to protect identities without creating unnecessary access barriers or accidentally locking administrators out of critical systems.

A strong Conditional Access design should:

- Protect privileged accounts with stronger authentication
- Require MFA for workforce identities
- Secure guest and external users
- Block legacy authentication methods
- Respond dynamically to risky sign-ins
- Use network location as an access signal
- Maintain emergency access
- Validate policy behavior before enforcement

The goal of this project was to build a small but realistic Conditional Access architecture following these principles.

---

## Environment and Tools

- Microsoft Entra ID
- Microsoft Entra ID P2
- Conditional Access
- Microsoft Entra ID Protection
- MFA
- B2B Collaboration
- Named Locations
- Conditional Access What If tool
- Emergency Access Account

---

# Conditional Access Architecture

## CA01 - Require MFA for Administrators

This policy protects privileged directory roles by requiring multifactor authentication when administrators access cloud resources.

The policy targets multiple privileged roles rather than individual users so the control scales as administrative assignments change.

A dedicated emergency access account was excluded to reduce the risk of tenant lockout.

![CA01 Admin MFA](screenshots/01-CA01-Admin-MFA-Policy.png)

### Design Decisions

- Targeted privileged directory roles
- Protected all cloud resources
- Required MFA
- Excluded the emergency access account
- Deployed in Report-only mode

---

## CA02 - Require MFA for Internal Users

This policy provides a baseline MFA requirement for workforce identities.

Internal users are protected separately from external B2B identities so different identity populations can be governed independently.

![CA02 User MFA](screenshots/02-CA02-User-MFA-Policy.png)

### Design Decisions

- Included all users
- Excluded the emergency access account
- Excluded the external B2B test identity
- Protected all cloud resources
- Required MFA
- Deployed in Report-only mode

---

## CA03 - Block Legacy Authentication

This policy blocks authentication attempts from legacy client types that do not fully support modern authentication controls.

Legacy authentication can bypass or weaken modern identity security controls, so removing these older authentication paths reduces credential-based attack exposure.

![CA03 Legacy Authentication](screenshots/03-CA03-Block-Legacy-Authentication.png)

### Design Decisions

- Included all users
- Excluded emergency access
- Protected all resources
- Targeted Exchange ActiveSync and other legacy clients
- Configured the policy to block access
- Deployed in Report-only mode

---

## CA04 - Require MFA for Guest and External Users

This policy protects B2B collaboration identities separately from the internal workforce.

External identities may authenticate through another organization, so separating guest access from employee policies provides cleaner identity governance and policy design.

![CA04 Guest External MFA](screenshots/04-CA04-Guest-External-MFA.png)

### Targeted Identity Types

- B2B collaboration guest users
- B2B collaboration member users

### Access Requirement

- Require multifactor authentication
- Protect all cloud resources
- Maintain emergency access exclusions
- Evaluate in Report-only mode

---

## CA05 - Require MFA for Risky Sign-Ins

This policy uses Microsoft Entra ID Protection risk signals to require additional authentication when a sign-in is considered suspicious.

The policy responds to **Medium** and **High** sign-in risk instead of treating every authentication event identically.

![CA05 Risky Sign In MFA](screenshots/05-CA05-Risky-SignIn-MFA.png)

### Design Decisions

- Included internal users
- Protected all cloud resources
- Targeted Medium and High sign-in risk
- Required MFA
- Deployed in Report-only mode

---

## CA06 - Require MFA from Untrusted Locations

A trusted named location was created using a known public network address.

This policy evaluates sign-ins from any location while excluding the trusted network. Sign-ins originating outside that trusted location are required to complete MFA.

![CA06 Untrusted Location MFA](screenshots/06-CA06-Untrusted-Location-MFA.png)

### Design Decisions

- Included internal users
- Protected all cloud resources
- Used network location as a Conditional Access signal
- Excluded the trusted named location
- Required MFA from untrusted locations
- Maintained emergency access
- Deployed in Report-only mode

---

# Policy Validation

## Admin Sign-In Validation

The Conditional Access **What If** tool was used to simulate an administrator accessing a Microsoft cloud resource using a normal browser session.

The test showed that multiple policies were evaluated simultaneously.

Applicable policies included:

- CA01 - Require MFA for Admins
- CA02 - Require MFA for Users
- CA06 - Require MFA from Untrusted Locations

![Admin What If Validation](screenshots/07-WhatIf-Admin-Policy-Validation.png)

This demonstrated that Conditional Access policies can work together rather than functioning as isolated controls.

---

## High-Risk Sign-In Validation

A second What If test simulated a **High-risk administrator sign-in**.

The following policies were evaluated:

- CA01 - Require MFA for Admins
- CA02 - Require MFA for Users
- CA05 - Require MFA for Risky Sign-Ins
- CA06 - Require MFA from Untrusted Locations

![High Risk What If Validation](screenshots/08-WhatIf-HighRisk-Policy-Validation.png)

This validated the risk-based Conditional Access design and confirmed that identity, role, risk, and location signals could be combined during policy evaluation.

---

# Final Policy Architecture

Six Conditional Access policies were created and kept in **Report-only mode** for safe testing and validation.

![Conditional Access Policy Overview](screenshots/09-Conditional-Access-Policy-Overview.png)

| Policy | Purpose |
|---|---|
| CA01 | Require MFA for privileged administrators |
| CA02 | Require MFA for internal users |
| CA03 | Block legacy authentication |
| CA04 | Require MFA for guest and external users |
| CA05 | Require MFA for risky sign-ins |
| CA06 | Require MFA from untrusted locations |

---

# Security Design Principles

## Least Privilege

Privileged identities were protected separately from normal workforce accounts.

## Emergency Access

A dedicated emergency access account was excluded from Conditional Access enforcement to reduce the risk of administrative lockout.

## Defense in Depth

The architecture uses multiple signals including:

- User identity
- Administrative role
- Authentication method
- External identity type
- Sign-in risk
- Network location

## Safe Deployment

Policies were configured in **Report-only mode** before enforcement.

This allows administrators to evaluate potential impact and identify unintended access problems before enabling policies in production.

## Separation of Identity Populations

Internal users, administrators, and external B2B identities were governed through separate policies rather than relying on one broad policy.

---

# Validation Results

The project successfully demonstrated:

- Privileged role targeting
- MFA requirements
- Emergency access exclusions
- Legacy authentication blocking
- External identity protection
- Risk-based Conditional Access
- Named location controls
- Report-only deployment
- What If testing
- Layered policy evaluation

---

# Skills Demonstrated

- Microsoft Entra ID
- Conditional Access
- Identity and Access Management
- Multifactor Authentication
- Microsoft Entra ID Protection
- Role-based access targeting
- B2B identity security
- Legacy authentication mitigation
- Named Locations
- Risk-based access policies
- Emergency access planning
- Identity troubleshooting
- Policy testing and validation
- Zero Trust identity concepts

---

# Lessons Learned

This project reinforced that Conditional Access is not simply an MFA configuration tool.

Conditional Access combines multiple identity and environmental signals to make access decisions.

A strong design requires understanding:

- Who is requesting access
- What resource they are accessing
- How they are authenticating
- Whether the authentication appears risky
- Where the request is coming from
- What controls should be required

The project also demonstrated the importance of testing policies before enforcement and maintaining emergency access paths.

---

# Future Improvements

Future improvements could include:

- Device compliance requirements through Microsoft Intune
- Authentication strength policies
- Phishing-resistant MFA requirements
- PIM integration for privileged roles
- Session controls
- Expanded Identity Protection remediation
- Production sign-in log monitoring
- Microsoft Sentinel integration
- Automated Conditional Access documentation using Microsoft Graph

---

## Security Notice

This project was created in a personal lab environment for cybersecurity and IAM portfolio development.

Sensitive credentials, authentication secrets, private network information, and production data are not included.

---

## Author

**Kaleb Dyess**

Information Science & Technology  
Identity & Access Management / Cybersecurity
