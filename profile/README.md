<p align="center">
  <img
    src="https://raw.githubusercontent.com/regtech-engineering/.github/main/assets/regtech-engineering-lab-avatar.png"
    alt="RegTech Engineering Lab"
    width="100"
  />
</p>

<h1 align="center">RegTech Engineering Lab</h1>

<p align="center">
  Open-source reference implementations for compliance automation, governed AI,
  financial-crime monitoring, KYC and regulatory change.
</p>

## What this is

Three systems covering the parts of compliance tooling that are awkward to build well: holding on to the evidence behind a decision, keeping a named human accountable for it, and reconstructing it a year later for an audit or a dispute.

Each is a standalone service owning its own data. They integrate over versioned APIs and domain events, not a shared operational database.

**Status:** early. What follows is the intended architecture. The repositories are still being built out.

## Projects

<table>
  <tr>
    <td align="center" valign="top" width="33%">
      <h3><a href="https://github.com/regtech-engineering/controlproof">ControlProof</a></h3>
      <p><strong>AI governance and decision control plane</strong></p>
      <p>Registry and approval workflow for the models, prompts, rules and datasets behind automated compliance decisions, plus the monitoring and kill switch on top of them.</p>
      <p><strong>Hard part:</strong> reconstructing which control version produced a given decision, months after it was made.</p>
    </td>
    <td align="center" valign="top" width="33%">
      <h3><a href="https://github.com/regtech-engineering/riskpulse-stream">RiskPulse Stream</a></h3>
      <p><strong>Real-time AML and fraud decision engine</strong></p>
      <p>Scores customer, merchant and payment events against versioned controls, correlates the resulting alerts and hands them to investigators as cases.</p>
      <p><strong>Hard part:</strong> event-time correctness — out-of-order arrival, idempotent scoring, and replay that reproduces the original outcome.</p>
    </td>
    <td align="center" valign="top" width="33%">
      <h3><a href="https://github.com/regtech-engineering/obligationgraph">ObligationGraph</a></h3>
      <p><strong>Regulatory change and KYC integration hub</strong></p>
      <p>Tracks authoritative sources, detects when they change, drafts the resulting obligations with citations, and maps them to affected systems and owners.</p>
      <p><strong>Hard part:</strong> grounding model output in cited source text, and degrading safely when a screening provider is slow or down.</p>
    </td>
  </tr>
</table>

## Architecture

<p align="center">
  <img
    src="https://raw.githubusercontent.com/regtech-engineering/.github/main/assets/regtech-engineering-lab-system-architecture.png"
    alt="RegTech Engineering Lab high-level system architecture, described in the text below."
    width="100%"
  />
</p>

Responsibilities split three ways:

1. **ControlProof governs.** Issues approved controls; receives evidence, quality signals, overrides and incidents back from the other two.
2. **ObligationGraph interprets.** Turns regulatory, sanctions and screening changes into approved obligations and implementation actions.
3. **RiskPulse Stream decides.** Evaluates live events, produces explainable outcomes, and returns case results.

Identity, observability, evidence storage, secrets and API/event contracts are shared across all three.

## Design rules

**Decisions carry their evidence.** A material decision retains its source data, the control and prompt versions that produced it, the machine output, and the human action and approval on top of it.

**Humans hold the authority.** Models classify, retrieve, summarise and recommend. High-impact decisions stay behind explicit roles, authority limits and reason codes.

**Failures are not clearances.** Timeout, unavailable, incomplete, malformed and no-match are five different outcomes. A dependency failing must never surface as a customer being cleared.

**Each service owns its data.** Stable published contracts, no shared databases, no cross-service writes as an integration shortcut.

**Controls are measured, not assumed.** Models and rules are evaluated against defined datasets and thresholds. Uptime is not evidence that a control works.

## Stack

| Concern | Choice |
| :--- | :--- |
| Services and APIs | C# / .NET 10 |
| Analyst and governance UI | React, TypeScript |
| Model development and evaluation | Python |
| Operational data | PostgreSQL, service-owned schemas |
| Event processing | Kafka for high-throughput streams, RabbitMQ for integration workflows |
| Supporting data services | Redis, ClickHouse, OpenSearch, S3-compatible object storage |
| Governance and orchestration | Open Policy Agent, MLflow, durable workflow tooling |
| Identity | OIDC, policy-based access, managed secrets |
| Observability | OpenTelemetry — metrics, logs, traces |
| Delivery | Docker, Kubernetes, IaC, CI/CD gates |

## What each repository should contain

- A bounded problem statement, an architecture overview and ADRs for the decisions that were close calls.
- Versioned API, event and data contracts.
- Synthetic data and scripted scenarios that run locally.
- Unit, integration, contract and resilience tests, plus evaluation results where a model is involved.
- Threat model, operating limits and documented failure semantics.
- A map from each claimed capability to the code and tests backing it.

## Scope

These are engineering reference implementations, not legal advice, regulatory approval or a production screening service. All customer, merchant, beneficial-owner, transaction and investigation data is synthetic.
