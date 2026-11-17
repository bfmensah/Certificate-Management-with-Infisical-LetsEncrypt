# CYber-Project1- Certificate Management with Infisical & Let's Encrypt
This project demonstrates how to build a centralized certificate management system using Infisical in a home lab environment. 
This mirrors real-world enterprise scenarios where organizations need a single source of truth for issuing, monitoring, and revoking TLS certificates across their infrastructure.

#The Problem This Solves

In enterprises, certificates are often scattered across teams and services. Without central management:

- Certificates expire unexpectedly causing outages

- No visibility into what's issued and where

- Manual processes lead to misconfiguration

- Revocation becomes a nightmare



What I Built

A self-hosted Infisical instance that:

   * Connects to Cloudflare for DNS-01 challenges

   * Uses Let's Encrypt as an external ACME Certificate Authority

   * Issues certificates through a standardized policy → profile → application workflow

   * Serves a real certificate to an application in my lab


🏗️ Architecture


Flow Summary:

  Infisical requests a certificate from Let's Encrypt (ACME)

  Let's Encrypt issues a DNS-01 challenge

  Infisical uses the Cloudflare connection to create/remove TXT records

  Certificate is issued and stored centrally in Infisical

  Application retrieves the certificate via API or ACME enrollment

📚 Prerequisites

Before starting, ensure you have:
Requirement	Purpose
Infisical instance (self-hosted or cloud)	Certificate Manager
Cloudflare account with a domain	DNS-01 challenge automation
A domain managed by Cloudflare	For certificate issuance
An application to consume the certificate	Real-world validation
🔧 Step-by-Step Implementation
Step 1: Create a Cloudflare App Connection

Infisical needs to complete the DNS-01 challenge by adding and removing TXT records on your behalf. This requires a Cloudflare API token with the right permissions.
1a. Create a Cloudflare API Token

  Log in to the Cloudflare dashboard

  Navigate to Profile → API Tokens → Create Token

  Configure the following permissions:

Permission	Scope	Why It's Needed
Zone - DNS - Edit	All zones (or specific zone)	Create/delete TXT records for DNS-01 challenge
Zone - Zone - Read	All zones	Verify zone details
Account - Account Settings - Read	Account	Verify account context

⚠️ Important: The API token is displayed only once. Save it securely (e.g., in a password manager).

1b. Obtain Your Cloudflare Account ID

  -On the Cloudflare dashboard homepage, click the account information dropdown

  -Select Copy account ID

1c. Create the Connection in Infisical

  In Infisical, go to Certificate Manager → Settings → App Connections

  Select Cloudflare Connection

  Enter your API Token and Account ID

  Click Connect

  ✅ Checkpoint: You should see a successful connection status. Infisical can now manage DNS records on your Cloudflare domain.

Step 2: Create an ACME External CA

With the Cloudflare connection in place, register Let's Encrypt as an external Certificate Authority.

  In Certificate Manager, navigate to Certificate Authorities

  Find the External Certificate Authorities section

  Click Create CA

  Select ACME as the CA type

  Configure:

  Name: Let's Encrypt (or similar)

  ACME Directory URL: https://acme-v02.api.letsencrypt.org/directory (production) or https://acme-staging-v02.api.letsencrypt.org/directory (staging/testing)

  Account Email: Your email for expiration notices

  Complete the creation wizard

  💡 Tip: Use the staging directory URL first to validate your setup without consuming Let's Encrypt production rate limits.

  ✅ Checkpoint: The external CA appears in your list and shows as healthy/connected.

Step 3: Configure the Certificate Issuance Workflow

After the external CA is created, complete the standard Infisical issuance workflow:
3a. Create a Certificate Policy

Policies define what can be issued (allowed domains, key algorithms, max TTL).

  Go to Certificate Manager → Policies → Create Policy

  Define:

  Allowed domains (e.g., *.lab.yourdomain.com)

  Key algorithm (e.g., RSA 2048 or ECDSA P-256)

  Maximum TTL (e.g., 90 days for Let's Encrypt)

  Save the policy

3b. Create a Certificate Profile

Profiles bundle a policy with a CA and defaults. They are the guardrails that ensure consistency.

  Go to Certificate Manager → Profiles → Create Profile

  Configure:

  Name: e.g., lab-lets-encrypt

  CA: Select your Let's Encrypt external CA

  Policy: Select the policy created in 3a

  Default TTL: e.g., 90 days

  Key algorithm: e.g., ECDSA P-256

  Save the profile

🎯 Why Profiles Matter: Teams no longer need to understand PKI internals. They simply pick a profile and request a certificate. The profile enforces CA choice, policy, TTL, and algorithm automatically — preventing duplicate setups and misconfigurations.

3c. Create an Application

Applications represent the services that will consume certificates.

 Go to Certificate Manager → Applications → Create Application

  Name it (e.g., homelab-web-app)

  Assign the profile created in 3b

3d. Configure Enrollment

Choose how the application will obtain its certificate:
Method	Best For
ACME Enrollment	Applications that support ACME natively (e.g., Traefik, Caddy, nginx with certbot)
API Enrollment	Custom automation, scripts, or apps that fetch certificates via Infisical API

For this project, I configured ACME enrollment so the application could auto-renew.
3e. Issue the Certificate

  Trigger issuance through the application's enrollment method

  Infisical orchestrates the DNS-01 challenge via Cloudflare

  Let's Encrypt validates the challenge

  Certificate is issued and stored in Infisical

  The application retrieves and serves the certificate

   ✅ Checkpoint: The certificate appears in Infisical with a valid status, correct expiry, and is being served by your application.

🎓 Key Concepts Explained
Concept	Simple Explanation
ACME	A protocol (used by Let's Encrypt) to automate certificate issuance and renewal
DNS-01 Challenge	Proving domain ownership by creating a specific TXT record in DNS
External CA	A certificate authority outside Infisical (like Let's Encrypt) that Infisical integrates with
Certificate Policy	Rules defining what certificates are allowed (domains, algorithms, TTL)
Certificate Profile	A reusable bundle of policy + CA + defaults that teams select from
Application	A service that consumes certificates via enrollment
🧠 Lessons Learned

  Central management is transformative — Having one place to see all certificates, their expiry, and status eliminates the "expired certificate outage" problem.

  DNS-01 is the most flexible challenge type — It works even when the service isn't publicly reachable (perfect for home labs behind NAT).

  Profiles are the enterprise unlock — They let teams self-serve without needing PKI expertise, while guardrails prevent misconfiguration.

  Staging first — Always test with Let's Encrypt staging to avoid rate-limit lockouts during setup.

  

🔮 Future Improvements

  

  Add certificate expiry alerting (email/Slack via Infisical)
  

  Implement automatic renewal verification for the lab application
  

  Integrate a second CA (e.g., ZeroSSL) for redundancy
  

  Document the revocation workflow for compromised certificates
  

  Add monitoring dashboards (Grafana) for certificate health
