<p align="center"><img src="https://raw.githubusercontent.com/cloud-pool-managers/brand/main/social/cloud-pool-managers.png" alt="cloud-pool-managers" width="760"></p>

# cloud-pool-managers

🌐 **[Website](https://cloud-pool-managers.github.io)** · 📚 **[Documentation](https://cloud-pool-managers.github.io/docs/)**

**On-demand pools of teaching virtual machines — in the browser, with zero install.**
A teacher creates a *pool* for a course; the platform provisions a fleet of identical
VMs on an OpenStack cloud; each student opens **JupyterLab**, **VS Code** or a terminal
straight from their browser — authenticated by their **Moodle**, **GitHub** or **SSH**
identity. Built at **École polytechnique — IDCS** to deploy a whole class's environment
in minutes instead of hours.

## What it does

- **Pools of VMs by course.** One action provisions *N* identical machines from a *golden
  image* (pre-baked JupyterLab / scientific stacks). Asynchronous job queue, **desired-vs-real
  reconciliation loop** (Kubernetes-style, self-healing), pool pre-warming.
- **Browser access, no install.** Per-student JupyterLab, **code-server (VS Code)** and a
  Guacamole terminal — all reverse-proxied over HTTPS; the VMs are never exposed directly.
- **Identity & enrolment.** OIDC (Dex/GLAuth), GitHub OAuth, SSH keys, and **Moodle / "cours
  de l'X"** course import — the student roster is pulled automatically.
- **Grading workflow.** Distribute, collect and auto-grade assignments with **nbgrader**, then
  push marks back to Moodle.
- **Real-time collaboration.** Live co-editing of a shared session (JupyterLab RTC), brokered
  on a dedicated infrastructure VM; password-based VS Code sharing between students.
- **Real-time UI.** PostgreSQL `LISTEN/NOTIFY` streams pool/VM state to the frontend — no refresh.
- **Operable & secure.** Prometheus + Loki + Grafana + OpenTelemetry; every API route
  authenticated with role checks, server-side identity, input validation.

## Architecture

Three tiers, gRPC throughout:

```
SvelteKit frontend  ──gRPC-Web──▶  Control Center (Go)  ──gRPC──▶  OpenStack microservice (Go)
                                         │                                  │
                                  PostgreSQL (state,                  OpenStack API
                                  LISTEN/NOTIFY)                      (gophercloud)
```

A **Caddy** reverse proxy terminates HTTPS and routes gRPC-Web, the REST API and the
application proxies (JupyterLab / code-server).

## Repositories

| Repository | What |
|---|---|
| **[vm-pool-managers](https://github.com/cloud-pool-managers/vm-pool-managers)** | The platform — control center, OpenStack microservice, SvelteKit frontend, Caddy |
| **[docs](https://github.com/cloud-pool-managers/docs)** | Documentation (MkDocs) → [cloud-pool-managers.github.io/docs](https://cloud-pool-managers.github.io/docs/) |
| **[cloud-pool-managers.github.io](https://github.com/cloud-pool-managers/cloud-pool-managers.github.io)** | Landing page (Hugo) |
| **[brand](https://github.com/cloud-pool-managers/brand)** | Logos and brand assets |

## Stack

Go · gRPC / gRPC-Web · SvelteKit (Svelte 5) · PostgreSQL · OpenStack (gophercloud) · Caddy ·
Dex / GLAuth (OIDC) · Guacamole · nbgrader · Moodle · Prometheus / Loki / Grafana / OpenTelemetry.
