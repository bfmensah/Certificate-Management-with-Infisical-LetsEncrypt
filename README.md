# Certificate Management with Infisical & Let's Encrypt
A home lab project demonstrating centralized TLS certificate management using Infisical, Cloudflare, and Let's Encrypt — mirroring how enterprises 
issue, monitor, and revoke certificates from a single source of truth.

📌 What This Project Does

This project deploys Infisical's Certificate Manager in a self-hosted lab environment and integrates it with:

   Cloudflare — for automated DNS-01 challenge validation

   Let's Encrypt — as an external ACME Certificate Authority

The result is a centralized system that:

   Issues certificates through a standardized Policy → Profile → Application workflow

   Automates DNS-01 challenges via a Cloudflare App Connection

   Serves a real, valid certificate to an application running in the lab

   Provides a single dashboard to track certificate status, expiry, and revocation
