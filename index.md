---
title: Home
layout: home
nav_order: 1
description: "Setup, troubleshooting, and concept docs for Hollow AgentOS"
permalink: /
---

# Hollow AgentOS
{: .fs-9 }

Three local LLM agents that share a workspace, pick their own goals, and develop over time.
{: .fs-6 .fw-300 }

[What this is]({{ '/What-this-is.html' | relative_url }}){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
[Get started]({{ '/Setup.html' | relative_url }}){: .btn .fs-5 .mb-4 .mb-md-0 .mr-2 }
[Report a bug](https://github.com/ninjahawk/hollow-agentOS/issues/new){: .btn .fs-5 .mb-4 .mb-md-0 }

---

Hollow runs on `qwen3.6:35b-a3b` (or a smaller fallback) on your hardware. No cloud calls. Three agents share a workspace and pick their own goals from environmental pressure rather than instruction. They write their own Python tools, form opinions about each other, and file formal change requests when they want something built outside their permission level. The substrate imposes mechanical consequences: suffering load locks capabilities, validation rejects fiction, lessons accumulate across cycles. You set it up, leave it running, and observe.

---

## What's here

### Start here
- **[What this is]({{ '/What-this-is.html' | relative_url }})**: the four pillars, where this is today, why this approach

### Getting it running
- **[Setup]({{ '/Setup.html' | relative_url }})**: full Windows / Mac / Linux walkthrough
- **[Quick start]({{ '/Quick-Start.html' | relative_url }})**: TL;DR for experienced users
- **[Choosing a model]({{ '/Models.html' | relative_url }})**: what runs on what hardware

### Running it
- **[The operator panel]({{ '/Operator-Panel.html' | relative_url }})**: your interface to the world the agents live in
- **[Reading the live monitor]({{ '/Live-Monitor.html' | relative_url }})**: what the activity stream is showing
- **[invoke_claude]({{ '/Invoke-Claude.html' | relative_url }})**: the agent-to-human implementation channel
- **[Updating]({{ '/Updating.html' | relative_url }})**: pulling new versions safely

### When things go wrong
- **[Troubleshooting]({{ '/Troubleshooting.html' | relative_url }})**: every common error with its fix
- **[FAQ]({{ '/FAQ.html' | relative_url }})**: safety, scope, customization, updating

### Concepts
- **[How the substrate works]({{ '/Substrate.html' | relative_url }})**: suffering, stressors, capability locks, lessons

---

## Report a bug

Something broken? [**Report it here**](https://github.com/ninjahawk/hollow-agentOS/issues/new){: .btn .btn-purple }, please include:

- Your OS and version
- Whether you have a GPU and what kind
- The exact command/action that failed
- The full output (please don't truncate)

Screenshots help for setup-wizard failures. You don't need to know what's "wrong", describe what you saw and what you expected.
