# Architecture Overview

This document describes the architecture of the "Shattered Minds" project, a self-hosted web platform designed and operated across local, cloud, and dedicated server environments.

The goal of this setup is to simulate a production-like infrastructure using real-world tools and workflows.

---

## High-Level Architecture

    GitHub (source code)
            ↓
    GitHub Actions (CI/CD)
            ↓
    Docker Images (GHCR)
            ↓
    Deployment targets:
       - AWS EC2 (k3s cluster)
       - Self-hosted server (Hetzner)
            ↓
    Web Application (Apache/PHP + MariaDB)

---

## Core Components

### 1. Application Layer

- Apache web server running PHP application
- MariaDB database for data persistence
- File upload system with storage on server

The application is designed to run both:

- directly on Linux (LAMP-style)
- inside Docker containers

---

### 2. Containerisation

The application is containerised using Docker.

Main components:

- Web container (Apache + PHP)
- Database container (MariaDB)

Example:

    docker compose up -d

This allows consistent environments across local and cloud deployments.

---

### 3. CI/CD Pipeline

GitHub Actions is used to automate build and deployment steps.

Pipeline responsibilities:

- Build Docker images
- Tag images (version + commit SHA)
- Push images to GitHub Container Registry (GHCR)

This ensures reproducibility and traceability of deployments.

---

### 4. Deployment Targets

#### AWS EC2 (k3s cluster)

- Lightweight Kubernetes (k3s)
- Deployments and Services used
- Images pulled from private GHCR registry
- Authentication handled via imagePullSecrets

Used for:

- learning orchestration
- testing self-healing behaviour

---

#### Self-hosted Server (Hetzner)

- Ubuntu Linux server
- Apache + MariaDB (native setup)
- Also used for Docker-based deployments

Used for:

- real-world hosting
- security hardening practice
- monitoring and logging

---

### 5. Networking

Key aspects:

- Port mapping (e.g. 8080 → 80)
- Internal Docker networking between containers
- DNS configuration for public domain
- HTTPS configuration with certificates

---

### 6. Security

Baseline security measures include:

- HTTPS with valid certificates
- HSTS and strict Content Security Policy
- Secure HTTP headers
- SSH hardening
- UFW firewall rules
- Fail2Ban for intrusion prevention

---

### 7. Monitoring & Logging

Observability stack includes:

- Filebeat → log shipping
- Wazuh → security monitoring
- Suricata → network-level inspection

Used for:

- detecting anomalies
- analysing system behaviour
- improving system visibility

---

## Design Principles

- Reproducibility → same setup across environments
- Isolation → containers separate application components
- Observability → logs and monitoring are essential
- Security-first → hardened configuration by default
- Incremental debugging → isolate and fix one issue at a time

---

## Key Learning Outcomes

- Understanding interaction between application, containers, and infrastructure
- Managing deployments across multiple environments
- Debugging real-world issues across networking and services
- Applying security hardening beyond default configurations
- Building a maintainable and structured infrastructure setup
