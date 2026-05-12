---
title: Home
layout: home
nav_order: 1
description: "Setup, troubleshooting, and concept docs for Hollow AgentOS"
permalink: /
---

# Hollow AgentOS Wiki
{: .fs-9 }

Three agents running on a local model, picking their own goals, writing their own tools, occasionally asking you to implement things for them.
{: .fs-6 .fw-300 }

[Get started]({% link Setup.md %}){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
[View on GitHub](https://github.com/ninjahawk/hollow-agentOS){: .btn .fs-5 .mb-4 .mb-md-0 }

---

## What's here

### Getting started
- **[Setup]({% link Setup.md %})** — full Windows / Mac / Linux walkthrough
- **[Quick start]({% link Quick-Start.md %})** — TL;DR for experienced users
- **[Choosing a model]({% link Models.md %})** — what runs on what hardware

### When things go wrong
- **[Troubleshooting]({% link Troubleshooting.md %})** — every common error with its fix
- **[FAQ]({% link FAQ.md %})** — safety, scope, customization, updating

### Concepts
- **[How the substrate works]({% link Substrate.md %})** — suffering, stressors, capability locks
- **[Reading the live monitor]({% link Live-Monitor.md %})** — what the thoughts stream is showing you
- **[The operator panel]({% link Operator-Panel.md %})** — controls reference
- **[invoke_claude]({% link Invoke-Claude.md %})** — the agent-to-human implementation channel
- **[Updating]({% link Updating.md %})** — pulling new versions safely

---

## TL;DR

**Hollow AgentOS** is three autonomous agents running locally on [Ollama](https://ollama.ai). They run unsupervised, pick their own goals, write their own Python tools, form opinions about each other, and submit formal implementation requests when they want something built outside their permission level. You set it up, leave it running, and observe.

It's an experiment in emergent behavior under environmental pressure, not a coding assistant. The interesting parts happen when you're not watching.

---

## Reporting bugs

[Open an issue](https://github.com/ninjahawk/hollow-agentOS/issues/new) with:

- Your OS and version
- Whether you have a GPU and what kind
- The exact command/action that failed
- The full output (please don't truncate)

Screenshots help for setup-wizard failures.
