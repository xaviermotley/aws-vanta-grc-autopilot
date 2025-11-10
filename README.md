# AWS ↔ Vanta GRC Autopilot  

**Event‑driven, serverless, OSCAL‑ready compliance pipeline**  

## Overview  

This repository demonstrates how to build an event‑driven, serverless pipeline that turns AWS compliance signals (AWS Config, Security Hub, Security Lake/OCSF) into live Vanta evidence & custom control status via the Vanta API. It also optionally updates Vanta Trust Center and exports OSCAL artifacts for auditors.  

### Why it matters  

- **Deep AWS + Vanta integration** – Vanta supports real‑time monitoring across dozens of AWS resources and compliance frameworks, enabling hands‑off evidence flow.  
- **Vanta API power** – Upload evidence, manage documents, query tests, and create custom resources via API.  
- **OCSF normalization** – AWS Security Lake uses the Open Cybersecurity Schema Framework so events are normalized and easier to process.  
- **Event‑driven architecture** – AWS Config and Security Hub emit events that can be routed with EventBridge Pipes and processed with Step Functions and Lambda.  
- **OSCAL compliance** – Provide machine‑readable control evidence for auditors using NIST’s OSCAL standards.  

### What you’ll build  

1. **Event sources (AWS)**  
   - AWS Config conformance pack events (ComplianceChange).  
   - Security Hub findings (ASFF mapped to OCSF).  
   - *(Optional)* Security Lake for centralized, normalized logs.  

2. **Event routing & orchestration**  
   - EventBridge Pipes filter and enrich events (e.g., NON_COMPLIANT / CRITICAL).  
   - Step Functions coordinate Lambda functions using Distributed Map for batch backfills.  

3. **Evidence & control automation (Vanta)**  
   - Generate JSON/PDF evidence and upload to the correct Vanta document via API.  
   - Create custom controls and resources in Vanta for bespoke requirements.  
   - Use custom resources + tests to drive pass/fail automatically.  
   - Query failing tests and trigger remediation tickets or Slack alerts.  

4. **Optional Trust Center & stakeholders**  
   - List Trust Center controls and surface a status board for demos.  
   - Control visibility for external stakeholders.  

5. **OSCAL export**  
   - Transform event and evidence metadata into OSCAL assessment results and component definitions for auditors.  

### Repository Structure  

```
aws-vanta-grc-autopilot/
├── infra/
│   ├── cdk/ or terraform/
│   │   └─ modules/
│   │       ├─ config_conformance_packs/
│   │       ├─ security_hub/
│   │       ├─ eventbridge_pipes/
│   │       ├─ step_functions/
│   │       └─ security_lake/
│   └─ envs/{dev,prod}/
├─ lambdas/
│   ├─ evidence_builder/
│   └─ vanta_client/
├─ mappings/
│   ├─ ocsf/
│   └─ oscal/
├─ docs/
│   ├─ architecture.md
│   └─ runbooks.md
├─ dashboards/
│   └─ cloudwatch/athena/
├─ .github/workflows/
│   └─ ci.yml
└─ README.md
```

### Metrics to showcase  

- **Evidence freshness** – Median minutes from AWS event to Vanta document submission.  
- **Autonomous coverage** – Percentage of Config rules and Security Hub controls automatically producing evidence.  
- **Closed‑loop rate** – Percentage of failing Vanta tests that get linked AWS signals, tickets, and ultimately pass.  
- **OSCAL artifacts** – Link to generated OSCAL JSON/YAML for at least one framework or control family.  

### Suggested implementation steps  

1. **Enable sources** – Activate Security Hub (choose guardrails) and deploy AWS Config conformance packs; optionally enable Security Lake.  
2. **Wire events** – Create EventBridge Pipes to capture AWS Config compliance changes and Security Hub findings; filter by severity and route to Step Functions.  
3. **Shape & submit evidence** – Generate compact evidence (rule/finding, account, region, resource, proof link, timestamp) and upload & submit via Vanta API; store returned IDs.  
4. **Make Vanta understand AWS signals** – Create custom controls; send custom resources to Vanta; query failing tests and drive remediation.  
5. **Publish & govern** – Export OSCAL assessment results; optionally update Trust Center control statuses.  

### Progressive add‑ons  

- **MCP “compliance copilot”** – Integrate Vanta’s MCP server for AI‑powered compliance Q&A.  
- **Evidence lineage** – Attach original OCSF record/ARN to evidence for audit chain‑of‑custody.  
- **Org‑scale backfills** – Use Step Functions Distributed Map for multi‑account backfills.  

### Notes & gotchas  

- Use Vanta OAuth client credentials and respect API rate limits; rotate secrets via Secrets Manager.  
- Trust Center APIs currently support listing/reading; treat publishing as a governed step.  
- Prefer using custom resources and tests to drive pass/fail rather than manually forcing state.
