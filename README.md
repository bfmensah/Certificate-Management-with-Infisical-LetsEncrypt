# Certificate Management with Infisical & Let's Encrypt

A home lab project demonstrating centralized TLS certificate management using **Infisical**, **Cloudflare**, and **Let's Encrypt** — mirroring how enterprises issue, monitor, and revoke certificates from a single source of truth.

---

## 📌 What This Project Does

This project deploys **Infisical's Certificate Manager** in a self-hosted lab environment and integrates it with:

- **Cloudflare** — for automated DNS-01 challenge validation
- **Let's Encrypt** — as an external ACME Certificate Authority

The result is a centralized system that:

1. Issues certificates through a standardized **Policy → Profile → Application** workflow
2. Automates DNS-01 challenges via a Cloudflare App Connection
3. Serves a real, valid certificate to an application running in the lab
4. Provides a single dashboard to track certificate status, expiry, and revocation

### Architecture at a Glance

```text
Infisical (Certificate Manager)
    ├── Certificate Policy  → defines allowed domains, algorithms, TTL
    ├── Certificate Profile → bundles policy + CA + defaults (guardrails)
    ├── Application         → the service consuming the certificate
    └── External CA         → Let's Encrypt (ACME)
              │
              ▼ DNS-01 Challenge
         Cloudflare (App Connection)
```
## Why This Project Is Useful
In real enterprises, certificates are often scattered across teams and services. Without central management:
* Certificates expire unexpectedly, causing outages
* No visibility into what's issued and where
* Manual processes lead to misconfiguration
* Revocation becomes a nightmare
  

This project demonstrates the fix: a single pane of glass for certificate lifecycle management.

## Who This Is For

| Audience | Value |
| --- | --- |
| **Recruiters** | Proof of hands-on PKI, ACME, and secrets-management experience |
| **Learners** | A replicable, step-by-step blueprint for building the same lab |
| **Homelabbers** | A practical way to stop juggling certbot cron jobs across services |
| **Security engineers** | A reference architecture for enterprise certificate governance |


💡 Key concepts demonstrated: ACME protocol, DNS-01 challenges, external CAs, certificate policies, certificate profiles, and application enrollment (API & ACME).
<br>
<br>


<details>
<summary><strong>Prerequisites</strong></summary>

<br>

Before starting, ensure you have:

| Requirement | Purpose |
| --- | --- |
| **Infisical instance (self-hosted or cloud)** | Certificate Manager |
| **Cloudflare account with a domain** | DNS-01 challenge automation |
| **A domain managed by Cloudflare** | For certificate issuance |
| **An application to consume the certificate** | Real-world validation |

</details>

<br>

## Implementation
Step 1: Create a Cloudflare App Connection

Infisical needs to complete the DNS-01 challenge by adding and removing TXT records on your behalf. This requires a Cloudflare API token with the right permissions.
1. **Create a Cloudflare API Token**: Log in to the Cloudflare dashboard, go to **Profile → API Tokens → Create Token**. Depending on your use case, configure the following permission sets :
    - **PKI (Certificate) use case**: Requires `Zone - DNS - Edit` and `Zone - Zone - Read and Account - Account Settings - Read` permissions.

<details>
<summary><strong>Required API Permissions</strong></summary>

<br>

| Permission | Scope | Why It's Needed |
| --- | --- | --- |
| **Zone - DNS - Edit** | All zones (or specific zone) | Create/delete TXT records for DNS-01 challenge |
| **Zone - Zone - Read** | All zones | Verify zone details |
| **Account - Account Settings - Read** | Account | Verify account context |

</details>
   
  - **⚠️ Important:**: This Token is only displayed once, be sure to save it securely.

    <img width="1920" height="1080" alt="new" src="https://github.com/user-attachments/assets/7a71cfa2-2721-4b35-9399-8bfb9645c603" />

    <img width="1920" height="1080" alt="2nd api-token" src="https://github.com/user-attachments/assets/7d89bfd2-4e85-4701-9e38-9894b759978a" />

Step 2: Obtain the Account ID- On the Cloudflare account homepage, click the account information drop down menu and select Copy account ID.
<br>
<br>
step 3: Create the Connection in Infisical: In Certificate Manager, go to Settings → App Connections, select Cloudflare Connection, enter the API Token and Account ID you just obtained, and click Connect to complete the setup. You can also do the same thing by going to integrations and accessing the *App Connections* to connect your Cloudflare to Infisical

<br>

<img width="3412" height="1942" alt="cloudflare-app-connection-form" src="https://github.com/user-attachments/assets/de823b96-f31a-47c2-a4d8-333e273820a6" />
<br>

<img width="1920" height="1080" alt="infisical conection" src="https://github.com/user-attachments/assets/611cc2ec-d083-4f29-b169-daca46f0a632" />

<br>

✅ Checkpoint: You should see a successful connection status. Infisical can now manage DNS records on your Cloudflare domain.

<br>
<br>
<br>

Step 2: Create an ACME External CA

<br>

After the connection is successful, you can register the external CA.
We need to register our external CA in order to create an acme (Automatic Certificate Management Environment) client that will request
for valid certificates from letsencrypt.

1. **Navigate to External CAs**: In Certificate Manager, go to **Certificate Authorities**, find the **External Certificate Authorities** section, and click Create CA
<br>

<img width="1920" height="1080" alt="Screenshot from 2026-10-04 03-30-11" src="https://github.com/user-attachments/assets/5bc9e118-acc2-4ce0-80e4-15dc28c6f8fe" />

<br>

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/49a277e1-a701-4c0a-8c59-4f8389a00d06" />

<br>

<img width="1920" height="1080" alt="Screenshot from 2026-10-04 04-10-33" src="https://github.com/user-attachments/assets/de2843c6-f26d-4fbe-be81-6568652fabd2" />


