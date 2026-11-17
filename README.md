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
