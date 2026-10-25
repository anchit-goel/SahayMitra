# Security Policy

## Prototype & Hackathon Notice

> [!WARNING]
> **SahayMitra is an experimental prototype built for Smart India Hackathon (SIH) 2025**. It is provided as-is for demonstration and research purposes and is **not audited or certified for production financial workloads**.
>
> **Known architectural limitations in v0.1.0:**
> - Gemini API keys are embedded or accessed directly from client-side runtime bundles.
> - Authentication uses mock user IDs without password hashing or session tokens.
> - E-KYC Aadhaar and OTP workflows are simulated on the client.
> - Data storage is held in volatile browser memory.

Do not use this codebase to process live personal identifiable information (PII), Aadhaar records, real bank credentials, or actual financial transactions without implementing a hardened, authenticated backend service.

---

## Supported Versions

| Version | Supported |
| :--- | :--- |
| `0.1.x` | :white_check_mark: (Prototype updates only) |
| `< 0.1.0` | :x: |

---

## Reporting a Security Vulnerability

If you discover a potential security vulnerability within this project (including unintended API key exposure, dangerous dependencies, or injection flaws), please report it responsibly:

1. **Do not open a public GitHub issue.**
2. Send an email to the maintainers at:
   <!-- TODO: Add security contact email, e.g., security@sahaymitra.example -->
   `security@placeholder.local`
3. Include detailed steps to reproduce the vulnerability, along with proof of concept code if available.

We will acknowledge receipt within 48 hours and work with you to understand and mitigate the issue.
