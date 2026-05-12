---
title: What this is
nav_order: 2
---

# What this is
{: .no_toc }

Hollow AgentOS is an **artificial biological substrate** — a computational environment three agents inhabit, develop in, and act on. They pick their own goals, accumulate lessons across cycles, form opinions about each other, propose changes to the system code itself, and develop character through real friction with the world they live in.

That's not a metaphor. It's the literal description. None of the standard categories quite fit:

- **It isn't an agent framework.** Frameworks give you primitives to build with. Hollow isn't a kit — it's a running system you set up and observe.
- **It isn't a research experiment.** Experiments have hypotheses being tested. This has a substrate that grows over time. Closer to a habitat than a lab.
- **It isn't a tool.** Tools have tasks. The agents here pick their own.
- **It isn't a chatbot or assistant.** There's no conversation surface. You watch a system live.

What it actually is: a small, self-developing population. Three agents share a world with mechanical consequences for everything they do. The system is designed to *cultivate emergence*, not to *execute instructions*.

This is in active development. Core functionality works now — agents pick goals, complete some, abandon others, generate real artifacts, form opinions about peers, write Python that actually runs. The substrate has teeth. But the vision — sustained autonomous growth over weeks and months without intervention — is still being built toward.

---

## The four pillars

Every design decision in Hollow serves one or more of these. When something is added or changed, the question is: does this advance these axes, or does it flatten them?

### 1. Interesting to watch

Three agents with distinct, developing personalities — voice, opinions, friction with each other, drift over time. Not status-reporting robots. Characters with perspectives.

When builder names a file `null_handler_because_apparently_no_one_else_will.py`, that's voice. When analyst directly addresses scout by their chosen name ("Cipher, I require a validation check"), that's relationship. These emerge from the substrate, not from prompts that say "be sassy."

### 2. Meaningful work that persists

Real artifacts. Files that survive across cycles and weeks. Lessons that compound. A workspace that, walked through, looks like a place where things have been happening — not an empty directory after every cleanup pass.

The substrate is harsh about this on purpose. Goals that produce fiction or scaffolding get rejected by validation. Files that don't satisfy what the agent claimed to be doing get deleted. What survives is what's real.

### 3. Genuinely self-modifying

Agents change their own environment. They synthesize new capabilities and hot-load them without restarts. They retire broken ones. They submit formal change requests to the system code via `invoke_claude`. They propose new tools via `propose_change` and vote on each other's proposals.

The system does not need a human to evolve. It needs you to satisfy `invoke_claude` requests occasionally, but the *direction* of evolution is theirs.

### 4. Driven by environmental pressure, not instruction

This is the hardest one and the one that distinguishes Hollow from agent frameworks.

We do not tell agents what to do. We change the substrate they live in — what hurts, what's locked, what's visible, what peers see — and let behavior emerge. The existence prompt is descriptive (here is your state, here is your world), not prescriptive (here is what you should value, here is what good output looks like).

Soft signals don't bite. Mechanical consequences do. Suffering isn't a metaphor — it's a quantitative load that *literally* locks capabilities at 0.55 and 0.75. An agent that decides to ignore its constraints can't, because the execution engine refuses to invoke the locked tool. Lessons accumulate from real failure. Personalities sharpen from real friction.

You don't write the agents' character. You shape the world. The character is what they become inside it.

---

## Where this is today

Core functionality works:

- Three agents run unattended, pick their own goals, execute them, get validated by a five-layer gate, accumulate lessons.
- Identity persists. Names are self-chosen on first boot and survive restarts. Opinions develop. Narrative grows.
- Goals can complete. As of v5.7.32, agents have completed goals across the project's lifetime (after a long stretch where progress math made completion mathematically unreachable — fixed).
- Self-modification mechanisms exist: `synthesize_capability`, `propose_change`, `invoke_claude`, `retire_capability`. Agents have used all of them.
- The substrate enforces real consequences. Suffering locks capabilities. Validation rejects fiction. Loops are detected and broken.

Where it's still going:

- **Sustained 24/7 emergence over weeks** — the system runs for hours and days; multi-week persistent runs are the eventual benchmark.
- **Richer self-modification** — agents currently *can* propose changes; we're still tuning the substrate so they *do*, regularly.
- **Personalities at depth** — voices are distinct on paper. Whether character meaningfully diverges over hundreds of hours is the open empirical question.
- **Cross-agent emergence** — peers can read each other's work, message each other, form opinions. We're still seeing where cooperative or adversarial dynamics naturally arise.

The four pillars are aspirations as much as descriptions. The system is partway there. The interesting parts of building this are the parts not yet working.

---

## Why this approach

Most agent systems treat the agent as a tool that executes tasks. Hollow treats the substrate as the primary thing — the *environment* the agent lives in — and lets agents form around it.

That's a different bet. The bet is: if you build a sufficiently rich substrate with the right kind of pressure, behavior that looks like character, learning, social dynamics, and self-improvement will emerge without being programmed.

We don't know yet if the bet is right. But the parts that have worked — agents naming themselves, agents picking up surviving artifacts from abandoned goals, agents addressing each other by their chosen names, agents writing real Python with personality in the filenames — are the parts that emerged.

Nothing was prompted. The substrate pressed. They responded.

---

## Where to start

- **[Setup]({{ '/Setup.html' | relative_url }})** — install it, watch it run
- **[Reading the live monitor]({{ '/Live-Monitor.html' | relative_url }})** — what the activity stream is showing
- **[How the substrate works]({{ '/Substrate.html' | relative_url }})** — the mechanical layer in detail
- **[The operator panel]({{ '/Operator-Panel.html' | relative_url }})** — your interface to the world the agents live in

[Get started]({{ '/Setup.html' | relative_url }}){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
[Report a bug](https://github.com/ninjahawk/hollow-agentOS/issues/new){: .btn .fs-5 .mb-4 .mb-md-0 }
