# Safe to Employ, Safe to Trust

### An architectural argument for the Agentic Readiness Stack

*By Bennett M. Reddin*

The [README](./README.md) is the map. This document is the argument: what each of the eighteen layers is, why it is hard, what it prevents, and how the layers relate as pillars and cross-cutting disciplines.

---

## 1. The assurance premise, and why agents break it

Enterprise software assurance has rested on one premise for roughly forty years: behavior can be specified before deployment. The premise has worn many methods. Design by contract made the specification explicit in the interface (Meyer 1992). Verification and validation split the question into "built right" and "built the right thing." Change control boards existed to notice when a specified system moved. Under all of them, trust was a property of the specification, and the specification did not change between Tuesday and Wednesday.

Agentic systems do not satisfy the premise. An agent's output is a function of its input, its retrieved context, its tool results, its accumulated memory, and the other agents it delegates to, and each of those varies at runtime. There is no fixed output contract to verify once and rely on thereafter. Pre-deployment evaluation retains value for models, prompts, and tools, but it cannot carry the assurance burden it carried for deterministic systems, because the artifact under governance is no longer only the output. It is the path: the authority used, the data touched, the calls made, the decision to act at all.

The consequence is architectural rather than procedural. If behavior cannot be specified in advance, assurance must attach to the system surrounding the agent: its identity, its permitted reach, its permitted beliefs, its stopping conditions, and the evidence it leaves. This document argues that the surrounding system decomposes into eighteen layers, and that readiness is the property of having all of them rather than the four to six that most organizations can currently claim.

A working definition anchors everything that follows.

**An agent is ready when three properties hold for every action it takes: the action is attributable to an identified actor and an explicit authority; bounded by limits established before the action rather than reconstructed after it; and provable, after the fact, to a party who was not present.**

Attributable. Bounded. Provable. Every layer in the stack serves at least one of the three, and the layer descriptions below state which.

---

## 2. Structure of the stack

Eighteen layers, two parts, six pillars, three cross-cutting disciplines.

**Part One (Layers 1-8)** is foundational infrastructure. These layers predate agents; the literature behind most of them is decades old, and the sections below cite it. What agents change is not the concepts but the operating conditions: request volumes, retry behavior, and the requirement that authority travel with every hop.

**Part Two (Layers 9-18)** is the agentic control plane: the layers that govern what an agent may believe, how much it may do unsupervised, and what it may claim to whom. These have thinner prior literature because their subject barely existed until recently. They are also where production failures concentrate, precisely because they are the layers a demo never exercises.

Within the parts, layers group into pillars. A pillar is the set of layers answering one underlying question, and each pillar restates an established body of theory for the agentic case:

| Pillar | Question | Layers | Restates |
|---|---|---|---|
| I. The Actor | Who is acting, with what standing? | 1, 2, 7 | Protection principles (Saltzer & Schroeder 1975) |
| II. The Gates | Through what mediation does action travel? | 3, 8 | Complete mediation; stability patterns (Nygard 2007/2018) |
| III. The Substrate | Is the data fit to reason on? | 5, 6 | Canonical modeling; information lineage |
| IV. Governed Belief | What may the agent come to believe? | 9, 10 | Belief revision (Alchourrón, Gärdenfors & Makinson 1985) |
| V. Earned Authority | How much may it do alone, and how does that change? | 11, 12, 13, 14 | Graduated automation (SAE J3016); compensating transactions (Garcia-Molina & Salem 1987) |
| VI. Accountable Speech | What may it claim, and to whom? | 16, 18 | Provenance (W3C PROV 2013); information-flow control (Bell & LaPadula 1973) |

Three layers are deliberately absent from the pillar table because they are cross-cutting: they are disciplines applied to every pillar rather than answers to one question. Layer 4 (observability) pairs with Layer 16 to form the stack's evidence spine. Layer 15 (structural coherence) monitors the shape of work across all agents and pillars. Layer 17 (idempotency) conditions every write the stack performs. Section 5 treats them together, along with the outward reach of Layer 13, whose action classification modulates the strictness of everything else.

Each layer receives the same four-part treatment: what it is, why it is hard, what it prevents, and a falsifiable self-test phrased as *you don't have this layer if*. The self-tests are the operational content of the framework; a readiness claim that cannot survive them is a readiness claim in name only.

---

## 3. Part One: Foundational Infrastructure

### Pillar I. The Actor (Layers 1, 2, 7)

The pillar restates the protection principles catalogued by Saltzer and Schroeder (1975): least privilege, complete mediation, and the separation of identity from mechanism. Nothing in the agentic case invalidates them. What the agentic case changes is the population of principals (software actors now outnumber human ones), the churn of their task assignments (hourly rather than quarterly), and the depth of delegation chains along which authority must remain traceable (Tomašev, Franklin & Osindero 2026).

#### Layer 1: Identity

**What it is.** Every request, human or agent, resolves to a verifiable actor with a distinct identity. An agent is a first-class principal: not a feature of its host application, not an extension of the invoking user's session, not a shared service account.

**Why it is hard.** Every low-effort path leads away from it. Reusing the invoking user's session is one line of code; a shared service credential works on day one. Distinct agent identity requires provisioning, lifecycle management, and a settled answer to what an agent identity denotes. The cost of the shortcut is deferred and compounding: it surfaces the first time an audit asks which of two agents performed an action and the access log offers one name for both.

**What it prevents.** Anonymous action, and the degradation it induces downstream. When the actor is ambiguous, authorization approximates, audit blurs, cost attribution guesses, and incident response begins with archaeology. Identity does not make an agent safe; it makes an agent addressable, and addressability is the precondition for every control that follows. Serves: attributable.

**You don't have this layer if** two of your agents would produce the same principal name in an access log.

#### Layer 2: Authorization & Policy Enforcement

**What it is.** Identity establishes who; policy establishes what they may do. For agents, authorization must be evaluated per action, in context, against the authority the agent carries for the current task. The relevant model is delegation with attenuation: authority granted for a task, scoped to that task, and narrowed at each hand-off, rather than a static role grant issued at deployment.

**Why it is hard.** Static grants are legible, auditable, and wrong for actors whose task set changes hourly. The steel-man for static grants is real: they are how every existing IAM system thinks, and they compose with existing review processes. The puncture is arithmetic. An agent acting a thousand times an hour under a grant sized for its broadest task is over-privileged in the majority of its actions, and over-privilege that is exercised at machine rate is not a latent risk but a running one.

**What it prevents.** The quiet accumulation of reach, and the category error of treating a policy misconfiguration as a support ticket. On a human timescale it is a ticket. At agent rate it is a breach with a timestamp for every instance.

**You don't have this layer if** the answer to "what may this agent do right now" is identical to the answer from three months ago.

#### Layer 7: Secrets Management

**What it is.** Credentials scoped to the narrowest function requiring them, rotated on schedule, revocable on demand, and never resident where an agent's code, context window, or memory could disclose them.

**Why it is hard.** The discipline is old; the attack surface is new. Agents multiply both the count of credential holders and the modes of disclosure, including a genuinely novel one: a language-model agent can be induced to reveal what it holds. Secret-at-rest architecture must therefore assume the agent itself is a potential disclosure channel, which rules out patterns (credentials in configuration, credentials in prompt) that passed review for deterministic services.

**What it prevents.** The compounding breach. In multi-tenant systems, one leaked credential is not one incident; it is one incident multiplied by every tenant within the credential's reach. Serves: bounded.

**You don't have this layer if** revoking one agent's access requires finding every location a key was pasted.

### Pillar II. The Gates (Layers 3, 8)

The pillar restates complete mediation (every access checked, no exempt path) and the stability-pattern literature that grew out of large-scale service operations (Nygard 2007/2018). Agents stress both harder than human-driven load ever did, because retry, loop, and fan-out are not exceptional behaviors for an agent. They are its ordinary texture.

#### Layer 3: API Management & Mediation

**What it is.** Agents act through gates that rate-limit, throttle, enforce contracts, and mediate access. No path from agent to system of record exists that cannot say no.

**Why it is hard.** Not conceptually; every architect knows gateways. The difficulty is the discipline of refusing exceptions: the internal tool exposed "just for the demo," the direct database handle that is "temporary." Each ungated path is a location where nothing stands between a retry loop and the infrastructure, and agentic retry behavior converts that gap from theoretical to scheduled.

**What it prevents.** Self-inflicted denial of service, contract drift, and the ungoverned side door. An agent capable of degrading its own enterprise's systems by trying hard is not a hypothetical failure mode; it is the default one.

**You don't have this layer if** any agent in the estate can reach a system of record without passing a policy enforcement point.

#### Layer 8: Integration Architecture

**What it is.** The pipes, queues, transforms, and contracts through which agents touch enterprise systems. The claim, defended from operations rather than aesthetics: the integration layer is not a prerequisite to the agent. Functionally, it is the agent, or at least the portion of the agent that performs consequential work.

**Why it is hard.** The steel-man is that most enterprises believe their integration layer is adequate, and by pre-agentic standards many are. The puncture is that agentic work changes the load profile in four ways at once: higher volume, machine-shaped retries, partial failure in the middle of multi-step plans, and the requirement that identity and authority propagate across every hop rather than terminating at the first queue. An integration layer scored against the old profile can fail all four tests while its dashboards stay green.

**What it prevents.** The capability mirage. A demo against a mocked endpoint measures nothing about production behavior, because production behavior lives in the integration layer's failure modes. Organizations that skip this layer discover, at scale, that their agent strategy was an integration backlog wearing a strategy's name.

**You don't have this layer if** the agent's plan survives the demo but not the third-party API's scheduled maintenance window.

### Pillar III. The Substrate (Layers 5, 6)

The pillar concerns the epistemic quality of what agents reason over. Canonical data modeling is unglamorous, politically expensive, and analytically prior to everything in Part Two: an agent's conclusions inherit the defects of its substrate, and no downstream control repairs an upstream falsehood.

#### Layer 5: Data Quality & Canonical Models

**What it is.** One coherent, governed representation of the domain, with data-quality discipline behind it, so that agents reason against a canonical model rather than a dozen vendor dialects that disagree at the margins.

**Why it is hard.** Canonical modeling is long work with no demo at the end, which is why it is the most commonly skipped layer in the stack. Its failure mode is also uniquely resistant to detection. A hallucination is at least occasionally identifiable by its strangeness. A fluent, well-formed answer computed from a stale or duplicated record is identifiable only by a reader who already knew the truth, which is precisely the reader the agent was meant to replace.

**What it prevents.** Confidently wrong output at scale: eloquent nonsense with citations to the wrong number. The distinction matters for governance because the two failure classes route differently. Hallucination is a model problem; confident wrongness on dirty data is an architecture problem, and only one of them is fixable with a better model.

**You don't have this layer if** two systems disagree about a basic fact of the domain and the agent has no way to know which is canonical.

#### Layer 6: Data Privacy & Compliance

**What it is.** Two enforcement points, jointly necessary: policy enforcement at the boundary where data moves, and lineage through the pipeline recording where data originated, what it influenced, and where it went.

**Why it is hard.** Most organizations hold one or neither. Boundary enforcement without lineage cannot answer "what did this record influence." Lineage without enforcement narrates violations elegantly after they occur. Agents sharpen the problem because they move data across purposes: a record lawfully retrieved for one task remains available, in context or memory, for the next task, and nothing but architecture separates permitted use from purpose drift.

**What it prevents.** Purpose drift, and the specific audit failure where every individual access was authorized and the aggregate use was a violation. Purpose limitation is a compliance obligation, not a courtesy, and it is enforceable only where lineage exists (cf. NIST AI RMF 2023; ISO/IEC 42001:2023 on accountability structures).

**You don't have this layer if** you can state who accessed a record but not what the access influenced.

### Cross-cutting in Part One: Layer 4

#### Layer 4: Observability & Audit Logging

**What it is.** Every agent action traceable: what was done, under whose authority, touching which data, with what result. The layer belongs to no pillar because it is the precondition for auditing all of them.

**Why it is hard.** Volume and semantics. Agents emit events at rates that exceed human-driven systems by orders of magnitude, and conventional logging answers "what happened to the system" where the governing question is "what was the agent attempting." Capturing intent, context, and authority alongside the action is a distinct logging discipline, and it is dramatically cheaper to build in than to retrofit, because the fields that matter are unrecoverable after the fact.

**What it prevents.** Governance by assertion. Absent a record sufficient to reconstruct an incident, every claim about agent behavior is a hope with a dashboard. Serves: provable.

**You don't have this layer if** reconstructing why an agent acted requires interviewing the engineer who deployed it.

Layer 4 records what happened. Its Part Two counterpart, Layer 16, records why it was concluded. Section 5 treats them as one spine.

---

## 4. Part Two: The Agentic Control Plane

### Pillar IV. Governed Belief (Layers 9, 10)

The pillar restates belief revision for production systems. The formal literature on theory change (Alchourrón, Gärdenfors & Makinson 1985) asked which propositions an agent should retain, revise, or retract as evidence arrives; the operational literature never needed the question until software began accumulating durable beliefs between sessions. Agent memory forces it. A memory store is not a cache to be filled but a belief set to be governed, and the difference is the difference between retrieval speed and epistemic integrity.

#### Layer 9: Agent Memory Governance

**What it is.** Write governance over what an agent may promote into durable memory. Observations pass through explicit policy: some may be written freely to working storage, some require confirmation before they harden into reference knowledge, some may never graduate past a scratchpad. Every write carries an immutable audit of the writing agent, its stated basis, and its confidence. The load-bearing distinction is that a scratchpad is not a system of record, and an architecture that cannot tell them apart has already conceded the layer.

**Why it is hard.** Ungoverned memory presents as a feature. An agent that "learns" by writing whatever it concludes improves visibly in demonstration and degrades invisibly in production, as one unvalidated inference becomes the premise of the next session's reasoning. The failure is autocatalytic, which distinguishes it from ordinary data-quality decay. It is also the layer with the least name recognition in the stack, so no stakeholder requests it before the degradation arrives.

**What it prevents.** Belief poisoning and compounding error, including the injection variant that arrives not through a prompt but through a stored memory the agent trusts because it wrote it. Prompt injection is a session-scoped attack; memory poisoning is a persistent one, and only write governance addresses persistence.

**You don't have this layer if** anything an agent concludes tonight is something it believes tomorrow, with no gate between the two.

#### Layer 10: Success & Failure Pattern Learning

**What it is.** An explicit outcome loop. Consequential actions produce recorded results; results update the confidence attached to the patterns that produced them; retrieval ranks by demonstrated performance, promoting what has worked and demoting what has failed.

**Why it is hard.** The steel-man is that the model itself improves, so explicit outcome tracking is redundant scaffolding. The puncture is scope: model improvement is global and slow, while the patterns that matter operationally (this vendor's API misbehaves under these conditions; this transform fails on this record shape) are local, fast-moving, and absent from any training corpus. Closing the local loop requires a per-task definition of success, which drags in Layer 12, and it requires recording failure durably and queryably, which most systems do only for debugging and never for the next attempt.

**What it prevents.** Confident repetition at scale. Without an outcome loop, an agent that found a plausible-but-wrong approach does not have a bad day. It has a bad career, re-executing the error with undiminished confidence each time the pattern matches.

**You don't have this layer if** an agent's confidence in an approach is the same after ten failures as after none.

### Pillar V. Earned Authority (Layers 11, 12, 13, 14)

The pillar restates graduated automation for enterprise agents. The precedent is explicit in other safety-critical domains: driving automation is stratified into defined levels with defined operational limits (SAE J3016), and no serious practitioner proposes granting the highest level on the strength of a demonstration. The pillar's four layers supply the equivalent stratification: a rank ladder, a definition of completion, a classification of consequence, and a stop mechanism. Together they answer the question deterministic systems never had to ask, because deterministic systems only ever did what they were told: how much may this actor do on its own judgment, and on what evidence does that change.

#### Layer 11: Staged Autonomy

**What it is.** Agents hold an explicit rank: Observer, then Recommender, then Gated-Executor, then Autonomous. Promotion is earned on measured evidence (acceptance rates, correction rates, behavioral stability over time), not granted on vendor claims or stakeholder enthusiasm. The ladder also has a ceiling clause: some agents, by design, never graduate, because for certain advisory contexts the ceiling is a policy decision rather than a performance gap.

**Why it is hard.** Institutional pressure runs one direction. The demonstration succeeded, the roadmap is ambitious, and every incentive argues for promotion. Holding an agent at Recommender until metrics justify Gated-Executor demands the same discipline as holding a junior employee's signing authority, applied to an actor that never complains and never knows what it does not know.

**What it prevents.** The default condition, which is that absent a tier model every deployed agent is effectively autonomous from day one. It also prevents the inverse failure: indefinite distrust that strands agents in demonstration purgatory because no defined path to authority exists. A graduation mechanism is as much an enabler as a brake.

**You don't have this layer if** the question "what would this agent need to demonstrate to act alone" has no written answer.

#### Layer 12: Closure Rules

**What it is.** A per-task-type definition of completion. Budget exhaustion is not task completion. "Finished" must resolve to one of four verifiable states: output validated against defined criteria, completion confirmed by the external system acted upon, a human signoff obtained, or the task explicitly escalated as unresolved. An agent that exhausted its steps did not finish. It stopped, and the distinction is the entire layer.

**Why it is hard.** Defining verifiable completion per task type is domain work without an owner, and the frictionless default (the agent reports success; everyone believes it) is always available. The exercise is also diagnostic in an uncomfortable way: asking "how would we know this is actually done" frequently reveals that the organization never knew, even for the human-performed version of the task.

**What it prevents.** Silent partial completion, arguably the most corrosive agentic failure mode: work that appears done, is reported done, and is not, discovered weeks later by whatever depended on it.

**You don't have this layer if** "the agent said it finished" is accepted as evidence that it did.

#### Layer 13: Reversibility Taxonomy

**What it is.** Every action available to an agent, classified before deployment: Reversible (an undo exists), Compensable (no undo, but a correcting action exists), Irreversible (neither). The taxonomy descends from the compensating-transaction literature (Garcia-Molina & Salem 1987), which formalized the middle category: long-running work that cannot be rolled back can often still be compensated. Approval rigor scales with the class, and Irreversible actions require human signoff regardless of the acting agent's rank, with prior state captured before execution.

**Why it is hard.** The classification work is honest labor that surfaces unwelcome findings, chief among them how many routine actions are quietly irreversible: the sent email, the external filing, the payment instruction. Teams performing the exercise discover their "low-risk" agent holds a dozen irreversible verbs nobody had named.

**What it prevents.** Rank outrunning blast radius. An agent trusted to act autonomously on reversible work must not inherit that trust for irreversible work, and this layer is what converts that sentence from aspiration to mechanism.

**You don't have this layer if** an agent's approval requirements are a function of the agent rather than of the action.

#### Layer 14: Kill Switches

**What it is.** The capacity to stop, with precision: halt an agent immediately, roll back its recent actions, freeze its spending, freeze its memory writes, hold its outbound communications, or demote its autonomy rank. Per agent, per tenant, per scope. And exercised under test, because the safety-engineering literature is unambiguous that an untested interlock is a diagram. Named is not executable.

**Why it is hard.** Stop mechanisms read as planning for failure and lose prioritization contests to features. The precision is also genuinely difficult. "Stop the agent" is trivial; "stop this agent's writes for this tenant while its read-only work continues" requires stop-paths designed into the architecture, because they cannot be appended to one.

**What it prevents.** The incident in which all parties agree the agent must be stopped and no party can state how, or in which the only available stop is a full outage for every tenant simultaneously.

**You don't have this layer if** the incident runbook's first step for a misbehaving agent is locating the engineer who deployed it.

### Pillar VI. Accountable Speech (Layers 16, 18)

The pillar governs claims rather than actions, and it draws on two distinct literatures. Provenance standards (W3C PROV 2013) and the evidentiary concept of chain of custody supply the model for Layer 16: a conclusion is accountable when the material that produced it is preserved and traceable. Information-flow control (Bell & LaPadula 1973) supplies the model for Layer 18: what may be disclosed is a function of the recipient and the classification, enforced by the system rather than the discretion of the discloser. Both layers exist because agentic speech is consequential in a way deterministic output never was: an agent's recommendation lands in a decision-maker's judgment, and its disclosure lands in a recipient's knowledge, and neither landing can be recalled.

#### Layer 16: Evidence Provenance

**What it is.** Audit records what an agent did; provenance records why it concluded what it did. For every consequential recommendation or action: which sources and memories were retrieved, at what confidence, composing what chain of reasoning, under what policy evaluation. Assembled at decision time, verifiable afterward, and sufficient for a hostile reviewer.

**Why it is hard.** Provenance must be captured in the moment. It cannot be reconstructed from ordinary logs, because the retrieval rankings and confidence states that shaped a conclusion are transient and are gone by the next session. The reasoning pipeline itself must therefore emit evidence as a design property, which is an architectural commitment rather than a logging configuration.

**What it prevents.** The unanswerable question. When a regulator, a board, or an affected party asks why the system concluded what it concluded, an audit log offers a shrug with timestamps. Provenance is the difference between producing the chain and asserting that the agent presumably had its reasons. Regulatory trajectories (NIST AI RMF 2023; ISO/IEC 42001:2023) point uniformly toward the former being demanded.

**You don't have this layer if** the explanation for an agent's recommendation would have to be regenerated rather than retrieved.

#### Layer 18: Communication Scope Enforcement

**What it is.** Authorization to speak, distinct from authorization to act: which stakeholders an agent may address, on which topics, at what data sensitivity, under what review requirements. Enforced as an architectural control on the output path, not requested as a paragraph of prompt.

**Why it is hard.** Speech is perceived as safer than action, so it is governed last, and prompt-level instruction resembles enforcement until context pressure displaces it. The substantive difficulty is that scope is relational: the identical fact is appropriate for one recipient and a violation for another, and the enforcement point must evaluate the pair, not the fact.

**What it prevents.** The agent that answers a junior employee with the executive briefing, discloses one tenant's pattern to another, or commits the organization in writing to terms no human approved. Action failures corrupt systems; speech failures corrupt trust, and of the two, trust restores more slowly.

**You don't have this layer if** the only barrier between an agent and the wrong recipient of a true fact is a sentence in its system prompt.

---

## 5. The cross-cutting disciplines

Three layers, and one pairing, operate across every pillar rather than within one. Their exclusion from the pillar table is deliberate: they are properties the whole architecture exhibits, not stations within it.

**The evidence spine: Layers 4 and 16.** Layer 4 (what happened) underlies all of Part One; Layer 16 (why it was concluded) underlies all of Part Two. Jointly they answer the demand every other layer eventually faces: prove it. Identity claims are proven by audit. Autonomy promotions are justified by recorded outcomes. Closure is demonstrated rather than asserted. Kill-switch invocations are reconstructable. A seventeen-layer stack without the evidence spine is a stack of assertions, and assertions do not survive audit, incident review, or discovery.

**Layer 15: Structural Coherence & Drift Detection.** The layer monitors the shape of the work itself: whether an agent's actions remain connected to its goals, whether a session is fragmenting into disconnected efforts, whether behavior drifts across sessions in a direction no one selected. The natural formalism is graph-theoretic. Model the execution as a graph of actions, dependencies, and goals, and degradation acquires measurable signatures: the graph fragments into disconnected components, or goal nodes become unreachable from the work actually performed. The layer is cross-cutting because drift is not the failure of any single control. Identity can hold, permissions can be honored, closures can be met, and the work can nonetheless cease to serve its purpose. Coherence signals are the leading indicator where every other layer provides lagging ones, and a leading indicator is the difference between detecting the iceberg and documenting the shipwreck.

**You don't have Layer 15 if** the first indication of an agent drifting off-goal would be a human noticing that the output got strange.

**Layer 17: Idempotency.** An operation is idempotent when applying it more than once yields the state of applying it once. The property is dictated by the delivery semantics of distributed infrastructure: queues deliver at least once, recoveries replay, and agents retry on ambiguity, so duplicate execution is not an edge case but a scheduled event. Every write therefore carries an idempotency key and a prior-execution check, wherever in the stack the write originates. The layer is unglamorous until a retry double-charges an account, double-sends an offer letter, or double-files a report, at which point it is briefly the only layer anyone discusses.

**You don't have Layer 17 if** replaying yesterday's queue would change today's data.

**The outward reach of Layer 13.** Reversibility resides in Pillar V, but its classification modulates the strictness of the entire stack: how much provenance a decision must carry, how senior a closure signoff must be, which autonomy rank may touch the action at all, how quickly a kill switch must be able to fire. Blast radius is the exchange rate among the layers. An architecture applying uniform rigor to reversible and irreversible actions has mispriced both: the reversible work is over-governed into uselessness, and the irreversible work is under-governed into exposure.

---

## 6. Proportionality

A readiness model that demands maximum strictness for every agent becomes approval theater, and approval theater is routed around, which is worse than honest under-governance because it is invisible. The stack is therefore graded, and Pillar V supplies the grading key. An Observer summarizing documents requires identity, mediation, substrate, and evidence, but its closure rules may be light and its stop mechanism simple. A Gated-Executor touching payroll requires everything, under test. Rigor follows authority and blast radius.

One element does not grade: the evidence spine. Attribution and proof are the properties that cannot be retrofitted after the incident that creates the demand for them, so they are the minimum for every rank, including Observer.

---

## 7. Scope: what this framework is not

This document defines readiness. It deliberately does not define implementation: no schemas, no product mappings, no vendor prescriptions, no deployment topologies. Those decisions belong to each organization's architecture, and a readiness model that smuggled one implementation inside itself would be an advertisement wearing a checklist.

The restriction is also an honest statement of what a framework can accomplish. A readiness model states what must be true before agents can be trusted with consequential work. Making it true, against real tenants, real regulators, and real data, is the work itself, and it is not performed by documents. Judgment described is a thought partner. Judgment executed under governance, against data that matters, is a platform. This document is the former, on purpose.

---

## 8. Conclusion

The argument compresses to three sentences. Agentic systems invalidate the specification premise on which forty years of software assurance rested, so assurance must relocate from the output to the architecture surrounding the actor. That architecture decomposes into eighteen layers across six pillars and three cross-cutting disciplines, each of which exists because a specific, nameable failure occurs in its absence. Readiness is the condition in which every action an agent takes is attributable, bounded, and provable, and the self-tests attached to each layer are the falsifiable form of that claim.

What does not compress is the older point underneath. No profession has ever produced trusted practitioners by talent alone; it produced them through institutions that supervised early work, bounded early mistakes, kept records sufficient to assign credit and fault, and retained the unconditional right to stop the work. The layers are those institutions, rebuilt for actors that act at machine rate. The capability arrived with the models. The trust has to be constructed.

---

## References

Alchourrón, C., Gärdenfors, P., & Makinson, D. (1985). On the logic of theory change: Partial meet contraction and revision functions. *Journal of Symbolic Logic*, 50(2).

Bell, D. E., & LaPadula, L. J. (1973). *Secure computer systems: Mathematical foundations*. MITRE Technical Report 2547.

Garcia-Molina, H., & Salem, K. (1987). Sagas. *Proceedings of ACM SIGMOD 1987*.

ISO/IEC 42001:2023. *Information technology: Artificial intelligence management system*.

Meyer, B. (1992). Applying "design by contract". *IEEE Computer*, 25(10).

NIST (2023). *Artificial Intelligence Risk Management Framework (AI RMF 1.0)*.

Nygard, M. (2018). *Release It! Design and Deploy Production-Ready Software* (2nd ed.). Pragmatic Bookshelf. (Original work published 2007.)

SAE International. *J3016: Taxonomy and definitions for terms related to driving automation systems*.

Saltzer, J. H., & Schroeder, M. D. (1975). The protection of information in computer systems. *Proceedings of the IEEE*, 63(9).

Tomašev, N., Franklin, M., & Osindero, S. (2026). *Intelligent AI Delegation*. Google DeepMind. arXiv:2602.11865.

W3C (2013). *PROV-DM: The PROV data model*. W3C Recommendation.

---

*The Agentic Readiness Stack © Bennett M. Reddin. Cite as: Reddin, B. — The Agentic Readiness Stack (18-Layer Framework).*
