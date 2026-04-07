# Deployment Guide

This document describes how the application is built, deployed, and run across different environments.

---

## Overview

The project supports multiple deployment approaches:

- Local Docker environment
- Self-hosted Linux server (Hetzner)
- Cloud deployment (AWS EC2 with k3s)

---

## Local Deployment (Docker)

Start containers locally:

    docker compose up -d

Verify running containers:

    docker ps

Access application:

    http://localhost:8080

---

## Build Process

Build Docker image manually:

    docker build -t web .

Or via Docker Compose:

    docker compose up -d --build

---

## Environment Configuration

Key environment variable:

- DB_HOST → must match database service name (e.g. `db`)

Example (PHP):

    $host = getenv('DB_HOST') ?: 'db';

---

## CI/CD (GitHub Actions)

Pipeline steps:

1. Trigger on push
2. Build Docker image
3. Tag image (latest + commit SHA)
4. Push to GHCR

This ensures consistent and reproducible builds.

---

## Deployment on Server (Hetzner)

Connect via SSH:

    ssh user@server-ip

Pull latest code:

    git pull

Start services:

    docker compose up -d

Check status:

    systemctl status apache2
    ufw status

---

## Kubernetes Deployment (k3s on AWS EC2)

Steps:

1. Configure cluster (k3s)
2. Apply manifests:

    kubectl apply -f deployment.yaml
    kubectl apply -f service.yaml

3. Verify pods:

    kubectl get pods

4. Check services:

    kubectl get svc

---

## Common Deployment Issues

### Container not running

Check:

    docker ps -a
    docker logs <container>

---

### Database connection failure

Check:

- DB_HOST value
- container names
- network configuration

---

### Port not accessible

Check:

- port mapping (docker)
- firewall rules (ufw)
- security groups (AWS)

---

## Best Practices

- Use environment variables (no hardcoding)
- Keep builds reproducible
- Separate app and database
- Use logs for debugging
- Deploy incrementally and verify each step
