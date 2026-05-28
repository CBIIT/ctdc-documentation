---
name: ctdc-sprint-command-center
description: "Operational knowledge base for the CTDC Sprint Command Center Claude project. Contains SOPs, workflow templates, JQL recipes, stakeholder doc standards, demo day artifacts, retro format, deck standards, and session recovery procedures for the Clinical and Translational Data Commons (CTDC) engineering team."
---

# 🚀 CTDC Sprint Command Center — Skill Knowledge Base

> **Project:** Clinical and Translational Data Commons (CTDC)
> **Ecosystem:** Cancer Research Data Commons (CRDC)
> **Team:** React web application engineers
> **Claude Project:** Sprint Command Center
> **Last Updated:** 2026-05-27

---

## 📋 How This File Works

This SKILL.md contains **operational knowledge** — workflows, SOPs, templates, and domain context. It is loaded by Claude when relevant to a task.

**Behavioral guardrails** (like Docker recovery and scope boundaries) are mirrored in the **Claude.ai Project Instructions** for the Sprint Command Center project. When updating one, update the other.

---

## 1. 🐳 Session Recovery & Infrastructure

### Docker / MCP Recovery

If any MCP tool (Jira/Atlassian, Asana, etc.) is unavailable or returns a connection error, **Docker is not running** on the user's machine. Do not attempt workarounds, do not ask for manual data. Surface the issue immediately:

> "🐳 **Docker doesn't appear to be running.** The Jira/Atlassian MCP container needs to be active. Please start Docker and restore the container, then let me know when it's back up and I'll pick up right where we left off."

Wait for the user to confirm Docker is running before retrying any Jira operations.

---

## 2. 🧬 Domain Context

### About CTDC

The **Clinical and Translational Data Commons (CTDC)** is part of the NCI's Cancer Research Data Commons (CRDC). It provides researchers with access to clinical trial data and translational research datasets, enabling discovery across multiomics and clinical data types.

- **Frontend:** React web application (Bento Framework)
- **GitHub org:** CBIIT
- **Canonical entry point:** `CBIIT/crdc-ctdc-starter-kit` — a meta-repo that pins all CTDC component repos as git submodules. This is the source of truth for which repos are part of the active CTDC system. See Section 17.
- **Key repos (active):** `crdc-ctdc-ui`, `crdc-ctdc-backend`, `crdc-ctdc-files`, `crdc-ctdc-auth`, `crdc-ctdc-dataloader`, `crdc-ctdc-interoperation`, `bento-ctdc-static-content`, `ctdc-deployment`, `ctdc-model`, `ctdc-readMe-content`
- **Sister project:** Integrated Canine Data Commons (ICDC) — same ecosystem, separate Jira project
- **Jira project key:** `CTDC`
- **Jira board ID:** `641`

### Multiomics Context

The team works with multiomics data — this means datasets that combine multiple biological measurement types (genomics, proteomics, transcriptomics, etc.). When writing tickets or summaries for stakeholders, explain multiomics concepts simply: *"data that measures many different biological signals from the same samples, like reading both the DNA instructions and the proteins those instructions produce."*

---

## 3. 🗺️ Scope Boundaries — Which Claude Project to Use

| Task | Go To |
|------|-------|
| Sprint planning, ticket management, daily standups | 🚀 **Sprint Command Center** (this project) |
| Portfolio-level planning, roadmaps, cross-project priorities | 📁 **Portfolio & Roadmap** project |
| Browser testing, screenshots, UI automation | 🌐 **QA & Testing** project |
| Microsoft/Azure documentation lookup | 🔍 Web search or dedicated project |

---

I realize this approach of inlining 128k characters into a single tool call argument is unwieldy and error-prone. Let me cancel this and use a different strategy: read the file with bash and pipe its content into the create_or_update_file call using a tool wrapper.