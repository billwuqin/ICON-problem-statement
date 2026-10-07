---
title: "Problem Statement for Observability, Intervention and Control (I&C) in Multi-Agent Autonomous Networks"
abbrev: "Observability and I&C"
category: info

docname: draft-wnd-opsawg-icon-ps-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
# area: AREA
# workgroup: WG Working Group
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
#  group: WG
#  type: Working Group
#  mail: WG@example.com
#  arch: https://example.com/WG
  github: "billwuqin/ICON-problem-statement"
  latest: "https://billwuqin.github.io/ICON-problem-statement/draft-wnd-opsawg-icon-ps.html"

author:
 -
    fullname: Qin Wu
    organization: Huawei
    email: bill.wu@huawei.com
 -
    fullname: Daniele Ceccarelli
    organization: Cisco
    email: daniele.ietf@gmail.com
 -
   fullname: Zhenqiang Li
   organization: CMCC
   email: li_zhenqiang@hotmail.com
 -
   fullname: Luis. M. Contreras
   organization: Telefonica
   email: luismiguel.contrerasmurillo@telefonica.com
 -
    fullname: Qiufang Ma
    organization: Huawei
    email: maqiufang1@huawei.com

normative:

informative:

  MCP:
    title: Model Context Protocol
    target: https://modelcontextprotocol.io/
    date: November 2024

  A2A:
    title: Agent2Agent (A2A) protocol
    target: https://google-a2a.github.io/A2A/#/documentation?id=agent2agent-protocol-a2a
    date: April 2025

  IG1507:
    title: IG1507 Intervention and Control for Agentic Operation V1.0.0 DRAFT
    target: https://projects.tmforum.org/wiki/pages/viewpage.action?pageId=411641744
    date: May 2026

  IG1251G:
    title: IP Network AN Level 4 Agentic Architecture for Multi-Scenario Autonomy
    target: https://projects.tmforum.org/wiki/pages/viewpage.action?pageId=401824956
    date: May 2026

--- abstract

This document provides an overview of the issues associated with the
deployment of the observability, intervention, and control of autonomous
agent pipelines in large-scale heterogeneous network environments. The
term "Intervention and Control" is used to describe a set of automated and
human-initiated mechanisms that guarantee the capability to observe, evaluate, constrain,
correct, and terminate Autonomous agents at any point, for any reason, irrespective of
their level of autonomy under which it operates, to ensure resilience, recovery,
and operational continuity.

The set of enabled observability, intervention and control reflects operator
service offerings to ensure that autonomous network operations can be stopped, or safely
redirected when required and is designed in conjunction with agent to agent, agent
to tools, agent to human interaction and service and network policy.

This document also identifies several key areas that the Agent Observability,
Intervention and Control group will investigate to guide its
architectural and protocol work and associated documents.

--- middle

# Introduction

Network operations are increasingly autonomous with the growth of network
management Agent applications at the network level and service level. The Agent lifecycle
management comprise the following phases:

- Agent Discovery: Discover capabilities and skills and onboard agent

- Agent Deployment: move agent from pilot project to production environments

- Agent Upgrade: Large language model, tools, prompts, memory related software update

To help network operators manage Network management AI agents with more consistency,
visibility and control, the observability component, intervention and control components
need to work in a collaborate manner and are critical for the Agent lifecycle management.

Since AI native operations may be non-deterministic, when network management agents
misbehave or deviate from what Agents are expected to do, current AI control
technologies (often referred to as "AI guardrails") are introduced to constrain
the behavior of AI agents within operational and compliance boundaries, prevent
AI from producing harmful results or taking wrong actions, e.g., escalate a decision
to a human for a high-risk network operation, defend against malicious attacks,
e.g., prompt injection. These AI guardrails enable you to do checks and validations of user
input and agent output and typically break down into input input guardrail, action guardrail,
output guardrail and operate at the input/output/pre-action filter level with static boundary
parameters. For example, image you have a network management agent that uses network active
and reactive assurance component to identify and resolve network issues, ensuring that any
disruption or degradations are promptly addressed. You wouldn't expect operational anomalies
or performannce drifts produce harmful results, leading to high risk network operation failure.
if the guardrail detects such operational anomalies, it can immediately raise an error and
prevent harmful results of high risk network operators.

However, as Agentic AI systems are increasingly integrated into autonomous workflows and
critical infrastructure, these static measures are proving insufficient for the
full operational lifecycle, e.g.,

- Unable to detect, interrupt, and rollover from unanticipated behaviors;

- Network operators usually lack an equivalent infrastructure or platform for human oversight;

- Provide continuous monitoring of an AI system's internal logic or its long-running
  execution paths that match the speed and scale of the network management Agent applications,
  e.g., network failure or security risk is hard to detect and control, occurring at machine speed.

- When a violation related to input/output filter is suspected, there are currently no standardized
  interoperable protocols for network operation and task intervention (e.g., immediate task suspension)
  and recovery (e.g., reverting to a last known safe state or undoing a series of autonomous actions
  that introduce substantial operational risk) mechanisms.

- In non-deterministic environments, the lack of human oversight and human-AI semantic intent exchange
  hinder timely risk mitigation and state recovery during boundary violations by agents.

This document provides a problem statement for protocol on continuous agent behavior observability, intervention and control.
We list the properties the protocol should have, then explain why those properties are necessary. We describe why a
new protocol is the best solution for the more general problem of identifying and characterizing trajectory records
related to agent behavior or workflow operation, continuous monitoring and evaluation, enable human oversight, provide
human and agent interaction for agent intervention and control at the service level and network level.

Where possible, any solutions work will be built in a modular way using existing IETF protocols.
However, no protocol solution choices will be made until the functional requirements have been
agreed, and then this will require an analysis of the capabilities of existing protocols and
identify gaps that need to be filled.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

-  Autonomous Agent: An AI-driven software entity capable of accepting
    a declarative goal and executing non-deterministic tasks.

-  Agent Drift: Gradual performance degradation or misalignment of
    reasoning patterns over time in a production environment.

-  Cascading Failure: A scenario where a failure in one downstream
    agent propagates across multi-agent boundaries.

- Human to Agent Communication: The interaction between human users and network management Agent designed to perform tasks, solve
    problems, or provide information. Unlike standard human-to-machine interaction where a human drives every step of a
    task, human-agent communication involves delegation, where the human provides a goal and the agent autonomously figures out
    how to achieve it.

- Agent Observability: The visibility into an agent's internal state, decision-making logic, and workflow execution from its
   external telemetry outputs (e.g., logs, traces, metrics), enabling human   operators or monitoring systems to understand what
   the agent is doing and why it behaves in a specific manner.

- Intervention: A reactive, emergency action to intervene or take control of an agent with boundary violations, anomalies, failures,
                or risks, so as to block harmful decisions, disrupt hazards, malicious abuse, and promptly mitigate losses.

- Control: Establish a deterministic operational boundary for the agent before execution. By pre-defining the agent's behavior scopes,
           operational constraints, and security baselines, it fundamentally mitigates abnormal behaviors from agents.

- Evaluation: Using Trajectory Record to assess the performance and understand how an Agent solves problems, e.g., checking if the
          agent took the shortest sequence of actions or wasted resources on redundant tools or analyzing specific segments of the
          trajectory to see if the agent excels at information retrieval but struggles with mathematical synthesis.

- Human Oversight: The practice of keeping humans actively involved in continuously monitoring of Autonomous agents. In agent trajectory
                   management, it ensures that network management agents do not go off the rails, violate safety protocols, or waste
                   resources. It transforms a fully autonomous "black box" into a controllable and collaborative system.

- Behavior: pattern of reasoning, decisions, and actions an Autonomous agent takes to achieve a specific goal such as reasoning sequence,
            the sequence and logic of execution paths.

- Trajectory Record: Keep track of Agent behaviour and produce audit log or trace information to Capture the entire "flight path"
                     or reasoning sequence the agent followed to reach its conclusion using a structured Thought,Action,Observation
                     loop.

# Problem Space

The deployment of autonomous agentic systems within operators' networks introduces fundamental operational,
architectural challenges. Current network management paradigms are static rule driven and therefore are built on
deterministic models that assume predictable, rule-based behaviors. The shift toward non-deterministic (probabilistic),
AI-driven network operation architectures creates a structural mismatch between machine-speed execution and human-speed
oversight. This gap manifests in the following three distinct aspects:

## The Observability Aspect

### Limited Transparency in Planning, Reasoning, and Tools Execution

As agents increasingly execute complex operational tasks (e.g., service
provisioning, fault diagnosis), they frequently delegate critical planning
paths and execution decisions to the underlying Large Language Models (LLMs).
This delegation creates an optimization barrier:

* **Lack of Trajectory Transparency:** Network operators cannot cannot
 validate the safety or intent of an agent's planned mutations and trace how
 an agent’s internal Chain-of-Thought (CoT) reasoning maps directly to mutating
 network configuration diffs (e.g., CLI changes or Network configuration changes)
 before or after execution. This may lead to unexpected consequence on the
 infrastructure.

* **Ambiguity of Data Attribution:** There is no standard mechanism to observe
   where context or knowledge come from. Operators cannot verify which external
   knowledge base, version, or retrieval weight led an agent to make a
   high-risk operational decision.

* **Invisible Tool Parameter Bindings:** Input and output states of invoked tools,
   scripts, and APIs are encapsulated within proprietary agent execution loops,
   preventing real-time validation of parameter bindings and performance evaluation.

### Ambiguity of Accountability or Responsibility Determination

In distributed agent interaction (e.g., agent to tools, human to agent, agent
to agent) topologies, operational responsibility for an ultimate network
outcome is scattered across an extended chain of coordinating agents,
foundational models, and abstraction layers. When system failures, performance
degradations, or unintended consequences occur, attributing accountability to
a specific agent entity, localized model decision, or human-in-the-loop anchor
becomes highly ambiguous. This lack of clear log, trace and performance metrics characterizing
agent operational health such as action execution latency, error rates, creates severe
complications for post-incident root-cause analysis and regulatory compliance reporting.

### High-Velocity Telemetry Data Ingestion Inefficiency for Evaluation and Intervening

Agents are explicitly designed to operate with high degrees of autonomy, speed, and
scale. However, network operators currently lack the corresponding telemetry
mechanism and human-on-the-loop infrastructure required to observe, evaluate, and intercept
these systems at the same machine-speed pace.  Consequently, effective
real-time oversight becomes functionally impossible. Always relying on human
escalation paradigms is usually impractical due to the sheer volume and velocity
of the data points involved in active agent pipelines:

* **Asynchronous Tracking and Clock Drift:** Distributed environments lack an authoritative
 causal event-ordering model. This prevents network operators from cleanly aligning internal agent
 reasoning loops chronologically with external network state telemetry changes.

* **Telemetry Storms:** Intensive CoT reasoning logs and high-frequency tool invocation
 traces can lead to telemetry storms that overwhelm Agent Observability Data collectors, yet legacy pipelines
 lack adaptive backpressure or dynamic sampling mechanisms.

* **Security vs. Auditing Trade-off:** Standard telemetry logging lacks context-aware, dynamic
 update. Network Operators are forced to choose between logging complete trajectories (risking
 the leakage of PII, credentials, or topology details) or omitting data (ruining post-incident
 accountability and cryptographic audit trails).

## The Control Aspect

### Inadequacy of Boundary Constraints and Risk Evaluation

Agent behavior cannot be reliably constrained using predefined, deterministic
rules or traditional static guardrails. Because agents rely on dynamic reasoning
patterns to achieve declarative goals, their exact execution trajectories remain
non-deterministic. This intrinsic variability bypasses legacy input/output filters
that fail to account for real-time contextual adaptation and makes operational
outcomes significantly less predictable during runtime execution, leading to
multi-step execution risks:

* **Implicit Impact Evaluation:** Network management agents lack a standardized
   protocol to evaluate and classify the risk tier (e.g., Low, Medium, High, Critical)
   or estimate the potential blast radius (affected devices, link traffic, customer
   scope) of an action sequence before deploying in production network.

* **Automation Storms:** Autonomous reasoning can lead to high-velocity loops.
    Without standardized per-device, per-operator access control, or global
   concurrency access controls and rate limits, agents run the risk of losing control
   on the network elements.

* **Undefined Action Cancellation Semantics:** Existing control frameworks do not
     natively support state-aware cancellation transitions,leading to partial,
     configurations or applied configuration without monitoring.

### Static Identity Management and Access Control

Traditional Identity and Access Management (IAM) frameworks were designed exclusively
for human operators or static, deterministic software processes. These frameworks cannot
securely tackle emerging dynamic agent attributes such as autonomous entity identities
and behavioral profiles which are frequently created and modified at runtime rather than
being pre-provisioned with metadata information to describe functional capabilities.

* **Absence of Role Separation:** Existing agent frameworks lack the mechanisms to enforce
   strict, credential Separation of Duties across distinct operational roles (Read-Only,
   Propose, Validate, Approve, Execute). This frequently allows a single agent to plan,
   approve, and execute a network configuration changes end-to-end without supervisory
   intervention.

* **Concurrency and Policy Conflicts:** In federated multi-vendor topologies, multi-agent
    operations lack distributed resource locking. When an action spans multiple network
    domains (e.g., routing vs. security), there is no deterministic framework to resolve
    cross-domain policy conflicts or enforce authority precedence (e.g., ensuring a Human
    Operator or Supervisor Agent explicitly preempts a lower-level autonomous agent).

* **Control Unreachability Vulnerability:** There is no standardized "fail-safe" or
    "fail-hold" expectation when an agent loses connectivity to its management plane,
    meaning a disconnected agent may continue altering network state entirely unconstrained.

* **Coarse Permission Demarcation:** Security policies cannot dynamically bind/constrain
    an agent's permissions to a specific agent in the network domain based on task context,
    nor can they safely manage downstream sub-agent permission delegation or context-dependent
    privilege escalation.

### Fragmentation Across Heterogeneous Integration Layers

Agentic systems operating across mixed Operational Support Systems (OSS)
and Network Management domains must interact with a highly heterogeneous
mix of legacy systems, modern APIs, and third-party platforms. Establishing
consistent, operational and compliance boundaries across these disparate
integration layers is exceptionally complex as agents may routinely validate intent,
invoke actions or retrieve data through pathways (e.g. MCP/A2A etc.) that were never
designed with network management automation we used today.

### Multi-Vendor Dependency Risks

As network operators begin sourcing agentic control capabilities from diverse third-party
vendors, independent software providers, and hyperscalers, operational
accountability becomes externalized in ways that are difficult to technically
or contractually enforce. A typical production agent implementation features
highly fragmented dependency chains spanning completely separate vendor
ecosystems, including LLM/AI model providers, infrastructure hosts, core
agent frameworks, tool/API repositories, and interconnection fabrics.

## The Intervention Aspect

### Lack of Human Oversight Capabilities such as Rollback and Recover

When an autonomous agent deviates from expected boundaries (e.g., entering
infinite reasoning loops, suffering from reasoning drift, or getting stuck
in deadlocks), the current human oversight capabilities including runtime
intervention for execution interruption, deterministic transaction rollback
and Recovery, and human escalation remain highly immature and lack
interoperability:

* **Lack of Out-of-Band Kill Switches:** The legacy Frameworks lack an
   asynchronous, out-of-band communication interface that can force an
   uncooperative or frozen agent execution loop to immediately pause or
   stop, completely independent of its internal LLM responsiveness.

* **Information Loss During Escalation:** When an agent encounters an
   ambiguous state and needs to escalate a problem to a higher human
   authority, it lacks an interoperable protocol to encapsulate and
   pass its full reasoning provenance and context trail seamlessly.

* **Lack of Granularity Control in Recovery:** Operators lack standard
   mechanisms to trigger targeted rollbacks. They cannot choose
   whether to revert a single execution step (Workflow level),
   a distinct operation (Task level), or an entire multi-step
   execution (Context level).

* **Dangerous Infrastructure Overrides:** Operators must rely on
   primitive infrastructure-level actions (e.g., killing a process,
   revoking an API key, or suspending a service account). These
   actions wipe out the runtime memory, provide no out-of-band
   state preservation, and allow no selective pausing or soft
   redirection.

Without human oversight capabilities, the existing network operation
can not ensure any disruptions or degradations are promptly addressed
which lead to harmful outcome for high risk network configuration
changes.

# Solution Space for Network Management Agent Observability, Intervention and Control

## Opentelemetry for Agent Observability and Drift Tracking

Modern agents orchestrate complex workflows: reasoning chains, tool execution, knowledge
retrieval, multi-agent collaboration. When things go wrong, or right, you need to understand
exactly what happened. Traditional network monitoring such as gRPC, SNMP, YANG Push
can't capture reasoning processes or decision context. Opentelemetry addresses this by utilizing
unified GenAI and Agent Semantic Conventions to standardise how metrics, logs, and distributed
traces are captured across multi-agent system.

Implementing OpenTelemetry for AI agents focuses heavily on distributed tracing and Context
Monitoring to how an agent processes information, arrives at decisions, and executes tasks
to establish baselines and identify drift early as follows:

- Distributed Tracing (Spans): The agent as a whole run acts as the root span. Every individual
  reasoning loop, agent delegation, LLM invocation, and tool/API execution is mapped as a child span.
  This layout instantly reveals where latencies, bottlenecks, or errors occur.

- GenAI Semantic Conventions: Standardised metadata tags provide explicit context. Spans automatically
  record critical variables across four critical domains: System Context, Token Economics, Vector Retrieval,
  and Agent Reasoning,like gen_ai.request.model, gen_ai.usage.input_tokens, and gen_ai.usage.output_tokens.

- Protocol, Decision and System Events: When opted-in, Opentelemetry logs every agent actions, every decision,
  every protocol communication between agents or between agent and tools. This visibility allows engineers to
  review the exact context that caused an agent to exhibit non-deterministic behavior or get stuck in an infinite
  loop.

## Context-Aware AI Guardrails and Validation

These are most mature, and most operationally familiar AI control mechanism in production today. AI Guardrail
approaches currently realized in the industry operate at defined transition points in the agent pipeline, primarily
prompt filtering at the LLM input boundary, response validation at the LLM output boundary, and access control
restrictions on tool invocation boundary. Currently, AI Guardrails are checks that run alongside your agents to catch
bad input or bad output — without necessarily involving your selected large language model(expensive or cheap).
For example, image you have a network management agent that uses network active
and reactive assurance component to identify and resolve network issues, ensuring that any
disruption or degradations are promptly addressed. You wouldn't expect operational anomalies
or performannce drifts produce harmful results, leading to high risk network operation failure.
if the guardrail detects such operational anomalies, it can immediately raise an error and
prevent harmful results of high risk network operators.

AI Guardrail are realized through 4 different mechanisms:

- Rule based filters: Apply pattern matching, keyword blocking, regular expressions, and deterministic logic to
  prompts, context retrieval and completions.

- LLM based safety classifiers: Use a secondary language model to evaluate the primary model's output for safety
  policy compliance before it is returned.

- Agent framework based guardrail libraries: Provide structured policy specification languages (e.g. NeMo Guardrails)
  that allow developers to express policy rules in a higher-level format, with the framework handling enforcement
  logic.

- Prompt engineering constraints: shape model behaviour by instruction rather than by interception.

AI Guardrails enforce policy constraints at boundaries to prevent non-deterministic or harmful network operations.
Take Multi-Step & Hybrid Defense as an example, AI Guardrail Combines rule-based filters, LLM safety classifiers,
and frameworks like NeMo Guardrails to evaluate multi-agent alignment across pipelines.

## Agent Drift Detection

Agent drift refers to the gradual degradation in an agent's performance and alignment with intended behavior over time
in production environments. It exists in many forms depending on the aspect of the system affected. For example, goal
drift occurs when the quality of goal execution deviates from expectations, context drift when the relevance or
accuracy of the working context deteriorates; reasoning drift when there is a decline in the agent's planning and
decision-making capability, and collaboration drift when the effectiveness of interactions with tools, external APIs,
or other agents degrades.

Agent drift is a well-recognized problem in academic research and agent frameworks. However, there is no universally
applicable control mechanism that addresses all scenarios in practice. A key reason for this is the
strong dependence on domain-specific expertise and observability mechanisms to detect, diagnose, and mitigate drift
effectively. Many of these also may require fine-tuning the base model with revised data sets. So a runtime control of
drift needs to be addressed in a case-by-case basis. Some of the practices followed for addressing the Agent drift are
as follows

- Goal drift is observed by statistical evaluation of production tasks against the evaluation tasks. The mitigation
  may involve fine-tuning the model with revised task lists and associated agent performance.

- Context drift is detected by monitoring the retrieved context, retrieval parameters/metrics and mitigated through
  context refresh, context window management and memory management.

- Reasoning drift is detected through metrics such as relevance, success rate, tool selection/usage, LLM-as-a-judge
  and it is mitigated through fine-tuning of models, optimizing the prompts/prompt engineering

- Collaboration drift is typically detected through interaction success rates across agent interactions and mitigated
  by fixing the issues with tool/API/agent interactions.

## Quality Gates for Human Oversight & Stage Transitions

Quality gates are checkpoints that evaluate whether an operation should proceed or not, or should be conditionally
allowed.

Unlike Guardrails which enforce policy constraints at defined boundaries of Agent implementation, Quality
Gates assess whether the work product of one stage meets a defined quality standard before permitting progression to
the next. The concept is borrowed from DevOps practice i.e. quality gates in CI/CD pipelines that prevent code from
advancing through build, test, and deployment stages unless it meets defined quality criteria. While guardrails determine
crossing points i.e. what enters and exits defined zones, quality gates determine progression points - whether work of
sufficient quality advances to the next stage.

Quality gates are the ideal mechanism to involve humans for agent tasks
execution quality asessment, i.e., at stage transitions where the accumulated work product of a whole reasoning
stage is ready for assessment where the human is presented with a complete plan, a complete risk assessment, and a
specific decision to make.

Currently none of the available agent frameworks have a named capability called quality gate. However, some of the
existing functionality can be leveraged for realizing this. For example LangGraph has concept of conditional edges that
enable dynamic, non-linear workflows which allows routing execution to different nodes based on a state-evaluating
function or if-else statements. Similarly, Google ADK (Agent Development Kit) provides callback that can be invoked
before or after tool use and implement quality gate logic. So most of the techniques that exist today are agent
framework specific.


### Existing Intervention Approaches for Human and Agent Interaction

Modern network agents orchestrate complex workflows that occasionally run into infinite reasoning loops, reasoning drifts,
or operational deadlocks. When boundaries are violated or an agent begins to misbehave, operators require a clear mechanism
to step in and pause, correct, or safely redirect the agent. Traditional infrastructure-level overrides (such as process
kills or API credential revocations) cannot gracefully manage context state, leaving network configurations partially applied.
Existing intervention mechanisms address this by establishing structured Human-in-the-Loop (HITL) communication models to
bridge the gap between autonomous task execution and external human oversight.

Implementing intervention approaches for AI agents focuses heavily on task suspension, state-aware recovery, and runtime
command injection to intercept failures early through the following mechanisms:

- Infrastructure-Level Controls (Repurposed): The most immediate, though primitive, intervention approach borrows
  standard IT infrastructure management actions. Operators forcefully apply process termination, API key revocation,
  or service account suspension to stop an agent. While highly effective at freezing uncooperative pipelines, these
  brute-force approaches wipe out the agent’s runtime memory and provide no mechanism for graceful recovery or
  selective sub-task modification.

- Framework-Specific Interaction Primitives: Modern orchestration frameworks provide embedded, code-level capabilities
  to pause and resume agent execution streams. For example, frameworks like LangGraph utilize native checkpointing states
  to let an agent pause its reasoning cycle, seek guidance or explicit input from a human, and subsequently proceed based
  on that human injection. Similarly, tools like CrewAI allow developers to define hard limits on loop iterations to
  prevent agents from spiraling into resource-wasting loops.

- External Kill-Switch Toolkits: Emerging control architectures, such as the Microsoft Agent Control Toolkit, introduce
  decoupled frameworks to monitor and forcefully halt an agent's execution externally when explicit trust, risk, or
  compliance boundaries are broken. However, these toolkit-driven intervention signals remain tightly bound to their
  native vendor platforms and lack proven interoperability when managing heterogeneous multi-vendor agent topologies
  across a production network.

## Existing Identity & Access Management Approaches

Modern network agents operate with high degrees of dynamic autonomy, executing complex cross-domain workflows that
alter live network states based on evolving context. Traditional Identity and Access Management (IAM) frameworks
cannot securely map to these systems, as they assume deterministic software processes or static human identities
with pre-provisioned metadata. Existing identity and access mechanisms address this mismatch by introducing
sandboxed runtime environments, short-lived tokens, and behavior-driven trust tracking to defend against malicious
input manipulation and unauthorized tool execution boundaries.

Implementing Identity & Access Management for AI agents focuses heavily on sandboxing, adaptive credentialing,
and runtime trust metrics to establish security boundaries and mitigate execution risks early as follows:

- Sandboxed Environment Isolation: A prominent current approach to manage agent risk is isolating the agent's
  runtime execution loop into secure sandboxes. Operators run the agent in a highly restricted environment so
  that it cannot cross structural trust boundaries or compromise broader critical infrastructure, even if the
  underlying model is actively manipulated.

- Static Privilege Control & Short-Lived Tokens: The most widely deployed baseline strategy applies static
  least-privilege principles expressed through standard IAM constructs like service accounts and OAuth scopes.
  To counter long-term credential leakage risks, best practices utilize short-lived, limited-access Json Web
  Tokens (JWTs) with narrow scopes and rapid expirations, restricting tool access to a highly compressed
  temporal window.

- Cntext-Aware and Dynamic Trust Level Assignment: Advanced emerging mechanisms move past static
  configurations by computing dynamic, behavior-driven trust scores in real time. Instead of labeling an
  agent as permanently trusted, frameworks (such as the Microsoft Agent Control Toolkit) dynamically
  track operational compliance; the score drops instantly upon policy violations, automatically shrinking
  permission profiles or restricting the agent's active trust zone.

## IETF AUDIT for Cross-Domain Interaction Traceability and Verification

Modern agents orchestrate complex, long-running workflows that frequently span multiple administrative
boundaries and invoke independent subagents or external tools without active human oversight at each step.
When anomalies or compliance violations occur, local agent logs are insufficient because no single
administrative party possesses full visibility over a multi-domain interaction chain. Traditional
auditing and logging techniques lack the cryptographic primitives required to produce verifiable proof
of execution to external, untrusted entities. The emerging IETF AUDIT approach addresses these limitations
by introducing standard data models and protocol-layer extensions to securely record, correlate, and
verify the multi-domain provenance of agent interactions while enforcing strict user privacy boundaries.

Implementing the IETF AUDIT framework focuses heavily on cross-domain correlation, dynamic authorization
tracking, and independent audit verifiability to track the entire action chain through the following
mechanisms:

- Cross-Domain Correlation and Common Identifiers: Rather than relying on isolated execution traces, the
  AUDIT framework adapts protocol-layer extensions (such as HTTP headers or context propagation tokens)
  to inject a common identifier across administrative boundaries. This allows an independent auditor to
  stitch together separate interaction records—spanning user intent, agent-to-agent delegation, and
  downstream tool invocations—into a singular, chronologically ordered action chain.

-  Time-Evolving Authorization and Identity Attestation: Unlike traditional static software processes,
   an agent's permissions shift non-deterministically based on runtime execution context and delegated
  trust boundaries. AUDIT leverages standardized data representations to capture authorization transitions
  as a continuous, time-evolving state, while cleanly differentiating between user, agent instance, and
  target service identities across the transaction history.

- Independent Verifiability and Privacy-Preserving Logging: To transform standard operational telemetry
   into a tamper-evident audit record, the architecture composes verifiable building blocks like
   attestation (RATS) and transparency logging (SCITT). Audit records are designed to be fully verifiable
   by a third party that trusts neither the agent nor its operator, utilizing strict selective disclosure
   mechanisms to omit sensitive prompt content or personal data while maintaining total cryptographic
   integrity.

# Gaps in the Current Approaches

## Gap in OpenTelemetry for Agent Observability

While OpenTelemetry (OTel) is the industry standard for collecting traces, metrics, and logs, it has critical limitations when
applied to AI agent observability. The fundamental limitation is that OpenTelemetry functions as a passive data plane for system
performance, not an evaluation or guardrail engine for AI behavior. It can track how an application runs, but it struggles to
evaluate what an agent decides.

Furthermore, OpenTelemetry only captures the execution process, not the operational motivation. It lacks native support for
observing metrics such as an agent's reasoning logic and internal confidence levels. OpenTelemetry originated in cloud-native
microservice architectures, its tracing lifecycle cannot represent asynchronous Human-in-the-Loop (HITL) workflows.
Consequently, it provides no mechanism to signal within a trace that a specific step constitutes a high-risk action,
has been suspended, and is currently awaiting human approval.

## Gap in AI Guardrails

There are many areas where guardrails cannot provide adequate control based on the current capabilities.

- Action focus: The majority of the guardrails focus on the text boundary whereas in agentic system the critical
  boundary is the action execution, i.e. the point where a tool call, API invocation, or database write reaches a
  live system.

- Multistep execution: Guardrails are typically applied at single-turn boundaries, i.e. they evaluate one input
  or one output at a time. Currently, there is no well-defined mechanism for evaluating the control implications
  of action sequences, or how a series of individually valid steps may collectively lead to unintended or non-compliant
  outcomes. This gap highlights the need for sequence-aware control and intervention mechanisms that can evaluate intent,
  track execution context across steps, and assess cumulative impact.

- Indirect instruction susceptibility: Agents are susceptible to security attacks, particularly those that exploit how
  context is constructed and consumed during execution. One prominent class of such attacks is prompt injection, which
  takes advantage of a key limitation in current guardrail architectures - i.e. the assumption that malicious or unauthorized
  instructions will appear only at the user input boundary. In reality, agentic systems ingest information from multiple
  sources, and instructions can be introduced indirectly through retrieved documents (RAG), tool outputs, system messages,
  or intermediate reasoning steps. Addressing this limitation requires a shift from boundary-focused guardrails to
  context-aware intervention and control mechanisms.

- Heavy human dependency:  Many guardrail implementations rely on human review for edge cases or escalations, which does not
  scale in high-speed or high-volume environments. Also, there is a fine balance required between flexibility of agent execution
  and reasoning, with the boundary of execution which is subjective.

## Gap in Agent Drift Analysis

Agent drift is hard to detect as it seldom produce a failure event, only the effect of the drift can be observed through
continuous monitoring. Current drift management techniques are retrospective and does not intercept the degraded agent
behaviour as it occurs or does not automatically adjust agent policy in response to detected drift, and does not coordinate
drift signals with runtime intervention mechanisms. Agent drift management has similarities to Anomaly management. So a
potential direction is to leverage some of the techniques used in Anomaly management applied to Agents.

## Gap in Quality gates

Three limitations characterize current implementation of quality gate.

- Implementation dependency on framework primitives. Quality gate behaviour is entirely developer-constructed from
  framework-specific primitives. There is no standard quality gate interface, no standard evaluation schema, no standard
  routing decision vocabulary, and no standard audit record format. A quality gate implemented in LangGraph is architecturally
  incompatible with one implemented in CrewAI or AutoGen. They cannot be governed, observed, or audited through common
  infrastructure.

- Absence of external observability. Quality gate evaluations are internal to the agent workflow. No current framework
  provides a standardised mechanism for an external observability authority to observe what quality evaluation was performed, what
  dimensions were assessed, what score was produced, and why a specific routing decision was made.

- No central intervention and control. Quality gate outcomes particularly human review and/or rejection decisions
  are not connected to a central control infrastructure. A gate that routes to human review pauses execution within the agent
  framework, but that pause is not expressed as a standardised intervention signal that a central control authority can monitor,
  escalate, or resolve. The gate operates in isolation from the broader management and control stack.

## Gap in the existing Intervention Approaches

As highlighted above intervention mechanisms exist in primitive and framework-specific forms. They have the following limitations.

- Absence of a standardised external interface that allows an authorized authority outside the framework or outside the
  agent application to signal intervention and receive a guaranteed response

- Current practice of intervention (leveraging infrastructure level interventions) is largely binary: either the agent runs or it
  does not. It lacks mechanisms that are flexible and applied across spectrum of scenarios - soft redirect, scope restriction,
  checkpoint, task suspension, rollback, hard termination

- When current intervention mechanisms stop an agent , whether through process termination, task cancellation, or API revocation,
  they do not systematically preserve the agent's execution state in a form that enables recovery.

- Current intervention mechanism requires either a human decision or a pre-coded condition to trigger it. There is no mechanism
  that continuously monitors agent behaviour against control policies and automatically triggers a proportionate intervention
  response when a deviation is detected

## Gap in the existing Identity and Access Management Approaches

From the I&C perspective following are some of the key limitations in incorporating identity and access management in the agents.

- Security control mechanisms primarily govern inputs and outputs, but have limited ability to fully interpret or validate the
  internal reasoning process of the agent. As a result, reasoning errors or misalignment may go undetected until they take effect
  through actions.

- In federated or multi-agent environments, enforcing consistent security and I&C policies across domains is complex. Differences
  in trust models, policies, and enforcement mechanisms can lead to gaps in control.

- Security standardization for agents is still evolving (e.g. OWASP Top 10 for Agentic Applications provide an emerging taxonomy
  of agent-related security risks) but they remain primarily focused on risk identification rather than operational control. At
  present, most control mechanisms are tightly coupled to specific frameworks or vendor implementations, leading to fragmented and
  non-interoperable approaches.
- Current frameworks do not provide a consistent approach to handle of delegated trust which is the trust relationship that arises
  when an orchestrating agent delegates authority to a sub-agent. For example, when a highly trusted orchestrator assigns a task to
  a sub-agent, it is unclear what level of trust the sub-agent should inherit, or what constraints should govern the delegated
  authority.

##  Gap in the IETF AUDIT Approach

There are several areas where the IETF AUDIT framework cannot provide comprehensive operational control or real-time risk
mitigation within autonomous network environments, based on its defined charter and design constraints:

- Exclusion of Internal Logic Assessment: The primary limitation of the AUDIT approach is its strict focus on external, observable
  behaviors and boundary interaction states. It explicitly excludes the auditing of underlying Large Language Models (LLMs),
  training sets, or internal inference parameters. Consequently, structural reasoning loops, hallucinations, or internal model
  drift remain completely invisible within the logged trajectory record.

- Retrospective Rather Than Interceptive Control: The AUDIT framework is fundamentally optimized for cryptographic evidence
  collection and post-event verification across trust domains. It completely lacks protocol-layer primitives to enforce
  inline runtime policies or execute real-time task redirection. This means a misbehaving agent generating high-velocity
  loop mutations cannot be actively intercepted or suspended before the configuration changes damage the network infrastructure.

- Absence of Active State Recovery & Rollback: While AUDIT focuses heavily on recording time-evolving authorization transitions
  and chain-of-custody data across distributed workflows, it lacks execution awareness. The framework specifies no mechanism to
  checkpoint active agent states, preserve runtime memory contexts, or trigger granular rollbacks (such as reverting a partial
   network configuration to a last known safe state) when a cross-domain policy violation occurs.

- Heavy Core Primitive Dependencies: The framework does not design or standardize standalone identity, attestation, or tracking
  primitives. Instead, it depends on the multi-group composition of external protocols (such as WIMSE, RATS, SCITT, and OAuth).
  This introduces severe deployment and synchronization risks; any fragmentation or architectural mismatch in those baseline
   blocks directly breaks the integrity of the end-to-end network automation audit trail.


# Standardization Area

This section outlines key areas where standardization is required to support the design, implementation, and operation of Network
Management Agent Observability, Intervention and Control in Agent Fabric networks. In the Agent Fabric Network,
- Two or multiple scenario specifc network management agents can work together to support multi-scenario autonomy or close loop management.
- Two or muitiple scenario specific network management agents can work together to support cross domain collaboration.
- Two or mutiple sceanrio specific network management agents can work together to support collaboration between service layer and network layer.
the agent gateway can be used to collect metric, log, audit information from each network management agents.

The intent is to identify foundational areas that require align with network management technologies developed in IETF OPS Area and drive network
automation moving toward AI Driven Network Operation.

- Agent Observability, Intervention and Control Network Management Architecture: Developing or selecting a framework for enforcing boundaries,
  detecting, evaluating, interrupting, correcting, and recovering from agent behavior within operational and compliance boundaries.

- OpenTelemetry protocol extension Enabling network behavioral assessment through analysis of observed operational network data
  (logs, metrics, traces, etc.)

- Human and Agent Interaction protocol for Human Escalation/Intervention, Agent Intervention and Control

# Security Considerations

The security considerations applicable to Network Digital Twin and Agentic AI based Architecture for AI driven Network Operations
{{?I-D.wmz-nmrg-agent-ndt-arch}} are also applicable to this document.

# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

The authors of this document would also like to thank Benoit Claise, Daniele Ceccarelli for review and comments.
