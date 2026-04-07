# Troubleshooting & Debugging Notes

This document contains real troubleshooting scenarios encountered while building and operating a self-hosted web platform across Docker, Linux servers, and cloud environments.

The focus is on practical debugging steps, root-cause analysis, and resolution strategies.

---

## Case 1 — Docker container not running

**Issue** 
Containers were not running when checking with:

    docker ps

No active containers were listed.

**Analysis** 
Checked all containers:

    docker ps -a

Found containers in `Exited` state.

**Cause** 
Containers had been stopped after previous runs and were not restarted.

**Resolution**

    docker compose up -d

Containers started successfully and ports were exposed correctly.

---

## Case 2 — Web container reachable but application not loading

**Issue** 
Application not accessible despite container running.

**Checks**
- Verified container status (`docker ps`)
- Checked port mapping (`0.0.0.0:8080->80`)
- Tested access via browser

**Cause** 
Application files not correctly copied into container.

**Resolution**
- Verified Dockerfile `COPY` instruction
- Rebuilt image:

    docker build -t web .
    docker compose up -d --build

Application became accessible.

---

## Case 3 — Database connectivity issue

**Issue** 
Application could not connect to MariaDB container.

**Symptoms**
- Connection errors in PHP
- Application failing silently

**Analysis**
- Checked environment variables
- Verified container names and network

**Cause** 
Incorrect `DB_HOST` value (using `localhost` instead of service name)

**Resolution**
Updated configuration:

    $host = getenv('DB_HOST') ?: 'db';

Connection restored.

---

## Case 4 — GitHub push authentication failure

**Issue**

    git push

returned:

    Invalid username or token

**Cause**
- GitHub no longer accepts password authentication
- Incorrect use of token in username field

**Resolution**
- Generated Personal Access Token (PAT)
- Used:
  - Username → GitHub username
  - Password → PAT

---

## Case 5 — Git permission issues on /var/www

**Issue**

    git init

returned permission errors.

**Cause**
Project files owned by `www-data`, not by user.

**Resolution**

    sudo mkdir .git
    sudo chown -R gabriele:gabriele .git
    git init

Also configured Git safe directory:

    git config --global --add safe.directory /var/www/cervelliesplosi

---

## Case 6 — Understanding container lifecycle

**Observation**
Containers stop or get removed, causing loss of runtime changes.

**Insight**
Containers are ephemeral and should not store persistent data.

**Solution**
Use volumes for persistence:

    docker volume create db_data

---

## Key Takeaways

- Always verify container state (`docker ps`, `docker logs`)
- Debug step-by-step, not by guessing
- Check networking and environment variables early
- Separate application logic from infrastructure issues
- Use documentation and logs before external tools
- Treat containers as disposable, not persistent

---

## Approach

When debugging issues:

1. Verify the problem using observable signals 
2. Identify the affected layer (network, container, application) 
3. Isolate variables (change one thing at a time) 
4. Validate the fix before proceeding further 
