# One Inversion, Two Maps

### Reading the Agentic Readiness Stack against Trusted AI Governance

*By Bennett M. Reddin*

This document compares the [Agentic Readiness Stack](./README.md) with Asanka Abeysinghe's [Trusted AI Governance](https://github.com/wso2/reference-architecture/blob/master/trusted-ai-governance.md), an architectural thesis for governing agentic systems in the enterprise. The comparison is offered in the spirit of the source: Abeysinghe's paper is deliberately vendor-neutral and invites exactly this kind of mapping. Where his model exposes gaps in the stack, this document says so plainly and records what the stack intends to adopt. Where the stack covers ground his model does not, it says that too.

---

## 1. The shared premise

Both frameworks begin from the same observation, reached independently: agentic systems break specification-based assurance.

Abeysinghe names it the inversion. Deterministic systems could be governed by approving the output before deployment, because a stable contract held at runtime. Agents have no fixed output contract, so governance must move to the boundary where intent becomes action and must produce evidence after the action. His compression of the consequence is exact: the enterprise does not trust the model; it trusts the system of control around the model. The agent inherits trust from the architecture around it.

The stack's [thesis](./THESIS.md) makes the same argument in different words: assurance must relocate from the output to the architecture surrounding the actor, and readiness is the condition in which every action is attributable, bounded, and provable. His "verifiable, attributable, and bounded" and the stack's "attributable, bounded, and provable" are near-identical triads, drafted without coordination.

The convergence is not a coincidence of vocabulary. Both frameworks descend from the same architectural lineage: the cell-based reference architecture (Abeysinghe & Fremantle 2018), in which governed units preserve local autonomy while enforcing policy at explicit ingress, egress, delegation, and evidence points. Abeysinghe co-authored that lineage; the stack's author has built within it for years. Two maps drawn from the same survey tradition should agree on the coastline, and they do.

Where they differ is altitude, and the difference is productive.

## 2. What each framework is

**Trusted AI Governance is a capability model for the enterprise estate.** Its unit of analysis is everything agentic operating in the environment, including the agents nobody approved: notebooks, CI/CD scripts, consultant prototypes, shadow AI in productivity tools. Its nine capabilities (discovery, identity and delegation, boundary authorization, runtime safety, human oversight, policy authorship and distribution, observability and evidence, economic control, and the integrated control architecture that binds them) describe what an enterprise must be able to do across that whole estate, regardless of vendor or platform. Its load-bearing architectural claim is the control plane and data plane split: policy authored centrally, enforced at distributed boundaries, proven through evidence.

**The Agentic Readiness Stack is a graded inventory for a deployment.** Its unit of analysis is a platform or system being built: the eighteen layers that must exist before the agents running on it can be trusted with consequential work, each with a falsifiable self-test. It assumes someone is constructing the thing being governed, which is why it can be concrete about mechanisms (write governance, closure semantics, reversibility classes, rank ladders) that an estate-wide model must leave abstract.

The steel-man for treating them as competitors is that they cover overlapping territory with different vocabularies, and frameworks compete for mindshare whether their authors intend it or not. The puncture is that their units of analysis do not overlap. An enterprise applies a capability model to govern an estate it mostly did not build. A platform team applies a readiness stack to grade a system it is building. The natural composition is nesting: the capability model governs the estate; each governed platform within the estate is graded by the stack. Neither substitutes for the other, and an enterprise that adopted both would find one integration seam, not a turf war.

## 3. The mapping

The table reads Abeysinghe's capabilities against the stack's layers. "Strong" means the stack instantiates the capability concretely. "Partial" means the stack covers part of the capability's scope. "Absent" means the capability has no layer, and the gap is real.

| Capability (Abeysinghe 2026) | Stack layers | Assessment |
|---|---|---|
| Discovery | none | **Absent.** The stack's Layer 1 is Identity, but discovery precedes identity: an enterprise cannot govern an agent it has not found. See Section 4. |
| Identity and delegation | 1, 2, 7 (Pillar I) | **Strong.** First-class agent identity, per-action authorization, delegation with attenuation. The stack and the capability model agree this is the keystone. |
| Boundary authorization | 2, 3, 18 | **Partial.** The stack has the boundaries (policy enforcement, mediation, communication scope). It is thinner on his parameter-aware enforcement: not "may this agent call the payment tool" but "may it refund this amount to this recipient." See Section 4. |
| Runtime safety (authorization is not safety) | 9, 15 | **Partial, and divergent.** The stack addresses the persistent form (memory poisoning, Layer 9) and the structural form (drift, Layer 15), but has no named layer for session-scoped manipulation: prompt injection, jailbreaks, permitted-but-wrong actions in the moment. Conversely, the capability model has no analogue for Layers 9 or 15. Each covers safety territory the other does not. |
| Human oversight | 11, 12, 13 | **Strong; the stack is more concrete.** His "graded control based on risk, authority, reversibility" is the stack's explicit machinery: the rank ladder, closure signoff modes, and the irreversibility gate. His principle that oversight is a deliberate seam rather than a failure fallback is exactly the stack's design stance. |
| Policy authorship and distribution | 2 (partially) | **Partial.** The stack's Layer 2 assumes policy exists and enforces it; it never asks who authors, versions, and distributes policy, or how regulatory obligations become enforceable rules. The control plane and data plane split is the capability model's strongest structural contribution, and the stack currently takes it as given. See Section 4. |
| Observability and evidence | 4, 16 | **Strong; the stack extends it.** His evidence requirement maps to the stack's evidence spine, and the stack's audit-versus-provenance distinction (what happened versus why it was concluded) sharpens a boundary his model gestures at. |
| Economic control | 12, 14 (partially) | **Partial.** The stack treats cost as a stopping condition (budget exhaustion is not completion; spending freezes exist among the kill switches). His model treats it as a governed dimension with per-agent attribution and a budget owner tied to a business outcome, which is more mature. See Section 4. |
| Integrated control architecture | Pillars + cross-cutting disciplines | **Structural agreement.** Both insist the controls compose rather than stack independently. His integration mechanism is the control plane; the stack's is the pillar structure plus the cross-cutting disciplines and the modulating reach of reversibility. |

Two enforcement observations sit behind the table.

First, Abeysinghe's inline versus infrastructure-enforced distinction has no counterpart in the stack, and it should. He argues that governance injected into agent code captures local intent but fragments across teams, while governance enforced by the surrounding fabric (gateways, meshes, identity infrastructure) provides consistency but loses context, so both are required and must be coordinated. The stack names eighteen layers but is silent on where each is enforced. That silence is a defect: two organizations could both claim Layer 12 while one enforces closure in every agent's code and the other enforces it at a shared boundary, and the difference matters enormously for assurance.

Second, the delegation literature both frameworks draw on is converging with them. The intelligent-delegation framework of Tomašev, Franklin and Osindero (2026) formalizes privilege attenuation for recursive sub-delegation, treats reversibility as a first-class task attribute requiring "stricter liability firebreaks and steeper authority gradients," and specifies verifiable task completion as a contractual requirement. Those are, respectively, the stack's Pillar I delegation model, Layer 13, and Layer 12, derived independently at protocol altitude. Three maps now agree on the coastline.

## 4. What the capability model presses on the stack

Four items, recorded here as the stack's open adoption ledger. Consistent with the repository's standing invitation (the fastest way to improve a readiness model is to have it fail against a real deployment and report back), these are acknowledged deficits with proposed dispositions, not settled changes.

**4.1 Discovery as Layer 0.** This is the capability model's single strongest correction. The stack begins at Identity, but identity makes agents addressable, and discovery finds them in the first place; an inventory that misses the notebook agent, the CI/CD script, and the consultant prototype yields identity coverage that is complete only over the agents someone remembered to register. The honest admission is that the stack was drafted from the perspective of a platform whose agents are all provisioned, where discovery is trivially satisfied by construction. The estate perspective breaks that assumption. Proposed disposition: add **Layer 0: Discovery** as a precondition standing outside the numbered stack (preserving the eighteen-layer structure), with the self-test *you don't have Layer 0 if the question "what agentic systems are operating in our environment" has no current answer.*

**4.2 Economic governance as a fourth cross-cutting discipline.** Cost currently appears in the stack twice, both times as a brake: budget exhaustion in Layer 12, spending freezes in Layer 14. Abeysinghe's treatment is broader: agents consume metered capacity non-deterministically, a malicious prompt can be an economic attack, and mature economic control means per-agent attribution, a budget owner, and a link to the business outcome the consumption serves. Rate limits contain damage; ownership answers whether the consumption was justified. Proposed disposition: promote economic governance to a cross-cutting discipline alongside the evidence spine, coherence, and idempotency, rather than adding a nineteenth layer.

**4.3 An enforcement-locus annotation.** Adopt the inline versus infrastructure-enforced lens as an annotation axis across all eighteen layers: for each, state where enforcement lives and what the failure mode is when it lives only in one place. This is documentation work rather than a structural change, and it directly addresses the defect noted in Section 3.

**4.4 Policy authorship under Layer 2.** Layer 2's definition should acknowledge the authoring question: policy that is enforced at boundaries must be authored, versioned, reviewed, and distributed somewhere, and regulatory obligations enter the system through that authoring function. The stack does not need to absorb the full control-plane apparatus to concede that "policy says what they may do" presumes a governed answer to who said so, in what version, effective when.

## 5. What the stack presses on the capability model

Recorded with the same directness, because the traffic runs both ways.

**5.1 Memory as a governed write surface.** The capability model treats memory and context as inputs that alter agent behavior, which they are. The stack's Layer 9 treats durable memory as a belief set requiring write governance, confirmation tiers, and immutable write audit, because the persistent form of the injection problem arrives through a stored memory the agent trusts, not through a prompt. Estate-level governance that inspects boundaries but not belief formation will pass an agent whose worldview was quietly poisoned last month.

**5.2 Drift as a governed dimension with leading indicators.** The capability model's evidence and audit machinery is post-action by design. The stack's Layer 15 adds a class of signal the capability model lacks entirely: structural coherence, the measurable connectedness of an agent's work to its goals, degrading before any boundary is violated. Every control in the capability model can hold while the work stops serving its purpose. A governance architecture needs at least one leading indicator, and coherence is the candidate.

**5.3 Closure semantics.** The capability model verifies outcomes and retains evidence; the stack's Layer 12 adds the sharper prior question of what constitutes completion per task type, with budget exhaustion explicitly disqualified. The distinction between "the agent stopped" and "the task closed" is invisible at capability altitude and decisive at deployment altitude.

**5.4 Idempotency.** No governance-altitude document names it, and every deployment eventually bleeds for it. Layer 17 is the stack's reminder that at-least-once delivery semantics make duplicate execution a scheduled event, and that a governance model whose evidence chain records a double-charge with perfect fidelity has documented a failure it should have prevented.

**5.5 Falsifiability as method.** The stack's per-layer self-tests (*you don't have this layer if...*) are its methodological contribution: a readiness claim is an empirical claim, and each layer states the observation that would refute it. Capability models describe; self-tests adjudicate. The two are better together, which is the point of this document.

## 6. The composition

Put the two frameworks in one architecture and the seams are clean. The capability model's discovery function inventories the estate and finds the platforms. Each platform is graded against the stack, self-tests and all. The capability model's control plane authors policy; the stack's Layer 2 and its enforcement-locus annotations describe how each platform enforces it, inline and at infrastructure. The capability model's evidence requirement is satisfied by each platform's evidence spine, and its economic-control requirement lands in the stack's (proposed) fourth cross-cutting discipline. Oversight thresholds authored centrally are executed by each platform's rank ladder, closure modes, and irreversibility gates.

Nothing in that composition required reconciling a disagreement, because the disagreement between the frameworks is close to zero. What differs is what each can see from where it stands. The estate view sees shadow AI, policy distribution, and cost ownership, and cannot see belief formation, drift onset, or closure semantics. The deployment view sees exactly the reverse. Both views descend from the same premise, state the same triad, and inherit the same lineage.

That is the finding worth stating plainly. When independently drafted frameworks, one from an enterprise CTO's vantage and one from a platform builder's, converge on the same inversion, the same trust definition, and complementary rather than conflicting controls, the convergence is evidence about the territory. The specification premise really is broken. Trust really has become an architectural outcome. The remaining question, and it is not a technical one, is the question both frameworks end on from their different altitudes: where accountability can credibly live when the actor is fast, tireless, and not a person. Neither map settles that. Both make it impossible to pretend the question is not on the table.

---

## References

Abeysinghe, A. (2026). *Trusted AI Governance: An architectural thesis for governing agentic systems*. WSO2 Reference Architecture. https://github.com/wso2/reference-architecture/blob/master/trusted-ai-governance.md

Abeysinghe, A., & Fremantle, P. (2018). *Cell-based architecture: A decentralized reference architecture for cloud-native applications*. WSO2 Reference Architecture. https://github.com/wso2/reference-architecture/blob/master/reference-architecture-cell-based.md

Reddin, B. (2026). *The Agentic Readiness Stack (18-Layer Framework)*. This repository: [README.md](./README.md) (map) and [THESIS.md](./THESIS.md) (argument).

Tomašev, N., Franklin, M., & Osindero, S. (2026). *Intelligent AI Delegation*. Google DeepMind. arXiv:2602.11865.

---

*The Agentic Readiness Stack © Bennett M. Reddin. Comparison text © Bennett M. Reddin; quoted framing from Abeysinghe (2026) is cited to its source and used for scholarly comparison.*
