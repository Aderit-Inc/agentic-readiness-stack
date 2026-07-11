# The Agentic Readiness Stack

**An 18-layer framework for taking an enterprise from "agents in a demo" to "agents in production you can actually trust."**

*By Bennett M. Reddin*

---

The stack isn't new. The urgency is.

Most organizations have pieces of four layers. Almost none have all eighteen.

Layers 1-8 are the infrastructure most enterprise architects already recognize. Layers 9-18 are the *agentic control plane*. They only become visible once agents are actually in production and failing in subtle ways.

An agent doesn't become trustworthy because it got talented. It becomes trustworthy because a system existed that could safely let it grow into the role: the right supervision, the right boundaries, and the right proof of what it did and why.

This is that system.

> This page is the map. The reasoning behind each layer, the pillar structure, and the cross-cutting relationships live in **[THESIS.md](./THESIS.md)**. A reading of the stack against Abeysinghe's *Trusted AI Governance* capability model, including the stack's open adoption ledger, lives in **[COMPARISON.md](./COMPARISON.md)**.

---

## Part One: Foundational Infrastructure (Layers 1-8)

*The infrastructure most enterprise architects already recognize: necessary, largely understood, and not where the hard part lives.*

1. **Identity**. Every request, human or agent, resolves to a verifiable actor. Without attribution there is no agentic enterprise.
2. **Authorization & Policy Enforcement**. Identity says who; policy says what they may do. A misconfigured policy is a breach, not a support ticket.
3. **API Management & Mediation**. Agents act through gates that rate-limit, throttle, and enforce contracts. Never directly against raw infrastructure.
4. **Observability & Audit Logging**. Every agent action is traceable: what it did, why, and which data it touched. The minimum viable trust layer.
5. **Data Quality & Canonical Models**. Agents reason against data; dirty data produces confidently wrong output, not obvious errors. The substrate everything else stands on.
6. **Data Privacy & Compliance**. Enforcement at the gateway *and* lineage through the pipeline. Most organizations have one or neither.
7. **Secrets Management**. Credentials scoped, rotated, and revocable. Existential the moment tenants share infrastructure.
8. **Integration Architecture**. In practice the integration layer isn't a prerequisite to the agent. It *is* the agent.

---

## Part Two: The Agentic Control Plane (Layers 9-18)

*The layers that stay invisible until agents are in production and failing in subtle ways. This is what separates a demo from a business.*

9. **Agent Memory Governance**. Memory is belief; write policies and immutable audit decide which beliefs an agent is allowed to form and keep.
10. **Success & Failure Pattern Learning**. An explicit outcome loop that updates confidence from results. Without it, you get confident repetition at scale.
11. **Staged Autonomy**. Agents graduate Observer to Recommender to Gated-Executor to Autonomous on measured metrics. Some, by design, never graduate.
12. **Closure Rules**. Budget exhaustion is not task completion; "finished" must mean validated, confirmed, signed off, or explicitly escalated.
13. **Reversibility Taxonomy**. Classify every action Reversible, Compensable, or Irreversible, and match approval rigor to blast radius.
14. **Kill Switches**. Halt, roll back, freeze, hold, demote. Per agent, per tenant, per scope, and *tested*. Named is not executable.
15. **Structural Coherence & Drift Detection**. Signals that catch an agent drifting off-goal early: the iceberg, not the shipwreck.
16. **Evidence Provenance**. Audit records *what* an agent did; provenance records *why* it concluded what it did. What regulators and boards will eventually demand.
17. **Idempotency**. An idempotency key and a prior-execution check on every write. Boring plumbing until a retry double-charges someone.
18. **Communication Scope Enforcement**. Authorization to *speak*, not just to act: which stakeholders, which topics, which data sensitivity. An architectural boundary, not a prompt instruction.

---

## How to read this

- **Layers 1-8** make an agent *safe to employ*. Most enterprises that have "done the AI infrastructure work" have stopped somewhere in this tier and believe they are ready.
- **Layers 9-18** make an agent *safe to trust unsupervised*. They are the layers that only reveal themselves after real agents are doing real work, and they are where credibility is won or lost.

If you have identity and a gateway, you have Layer 1 and part of Layer 2. That is the beginning of the stack, not the whole of it.

For the full treatment of each layer, the pillar structure, and which layers cut across the whole stack, read **[THESIS.md](./THESIS.md)**.

---

## Selected principles

> Memory isn't a cache. It's a system of belief, and beliefs need governance.

> Named kill switches are not executable kill switches.

> Audit tells you what an agent did. Provenance tells you why it concluded what it did.

> The stack isn't new. The urgency is.

---

## Status & contribution

This is a living framework. Layers, definitions, and stakes are refined as agentic systems move further into production. Issues, counter-arguments, and layer proposals are welcome. The fastest way to improve a readiness model is to have it fail against a real deployment and report back.

## License

This repository is published under the MIT License (see [LICENSE](./LICENSE)).

## Attribution

The Agentic Readiness Stack © Bennett M. Reddin. Cite as: *Reddin, B., The Agentic Readiness Stack (18-Layer Framework).*
