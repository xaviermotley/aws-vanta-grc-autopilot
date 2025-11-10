# 👀 AWS ↔ Vanta GRC Autopilot  
**Event‑Driven, Serverless, OSCAL‑Ready Compliance Automation**  

[![Built with AWS Security Services](https://img.shields.io/badge/Built%20with-AWS%20Security%20Services-orange?logo=amazonaws)](https://aws.amazon.com/) [![OCSF Compliant](https://img.shields.io/badge/Data%20Model-OCSF-blue?logo=data:image/svg+xml;base64,PHN2ZyBmaWxsPSIjZmZmIiB2aWV3Qm94PSIwIDAgMjAgMjAiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI+PHJlY3Qgd2lkdGg9IjIwIiBoZWlnaHQ9IjIwIiByeD0iMyIgZmlsbD0iIzAwN0JGNCIvPjx0ZXh0IHg9IjUiIHk9IjE1IiBmb250LXNpemU9IjEwIiBmaWxsPSIjZmZmIj5PQzwvdGV4dD48L3N2Zz4=)](https://schema.ocsf.io/) [![Event‑Driven Serverless](https://img.shields.io/badge/CI%2FCD-Event--Driven--Serverless-success?logo=githubactions&logoColor=white)](https://aws.amazon.com/eventbridge/) [![Vanta API Integrated](https://img.shields.io/badge/Integration-Vanta%20API-purple)](https://developers.vanta.com/) [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## 📚 Overview  
This project builds an event‑driven, serverless pipeline that turns **AWS compliance signals** (AWS Config, Security Hub, and optionally Security Lake/OCSF) into live **Vanta evidence** and **custom control status**. The pipeline collects compliance events, routes them through EventBridge and Step Functions, builds evidence artifacts via Lambda, and submits them to Vanta via the Vanta API. It optionally updates the Vanta Trust Center and exports **OSCAL** artifacts for auditors.  

## ⚡️ Quickstart  
Clone the repo and bootstrap the infrastructure (Terraform/CDK). Then enable AWS Config conformance packs and Security Hub in your AWS account. Configure your Vanta OAuth credentials in AWS Secrets Manager. After deployment, compliance events will automatically flow from AWS to Vanta.  

```bash
git clone https://github.com/xaviermotley/aws-vanta-grc-autopilot.git
cd aws-vanta-grc-autopilot
# deploy infrastructure via terraform or cdk (see infra/)
# configure Vanta OAuth secrets in AWS Secrets Manager
# monitor logs and evidence freshness metrics
```

## 🏗️ Architecture  
For details and diagrams, see `docs/architecture.md`. In summary, AWS compliance services emit events that are filtered and enriched via EventBridge Pipes and processed by Step Functions. The state machine invokes two Lambdas: **evidence_builder** to render evidence JSON/PDF and save to S3, and **vanta_client** to upload/submit evidence and manage custom controls/resources via the Vanta API. Optionally, events are also stored in Security Lake/OCSF for analytics and exported to OSCAL.  

## 📂 Repository Structure  
```
aws-vanta-grc-autopilot/
├── infra/                   # Infrastructure modules (Terraform/CDK)
├── lambdas/                # Evidence builder & Vanta client code
├── mappings/               # OCSF and OSCAL transformation maps
├── docs/                   # Architecture diagram and runbooks
├── dashboards/             # CloudWatch or Athena queries
└── .github/workflows/ci.yml  # CI pipeline
```

## 📊 Key Metrics  
- **Evidence freshness** – median time from AWS event to Vanta evidence submission.  
- **Autonomous coverage** – percentage of Config rules and Security Hub controls automatically producing evidence.  
- **Closed‑loop rate** – percentage of failing Vanta tests with linked AWS signals that are remediated via tickets.  
- **OSCAL artifacts** – availability of machine‑readable OSCAL exports for frameworks or control families.  

## 🚀 Implementation Steps  
1. **Enable sources**  
   - Turn on Security Hub and select guardrails (e.g., CIS/NIST).  
   - Deploy AWS Config conformance packs across your organization.  
   - (Optional) Enable Security Lake for normalized OCSF telemetry.  
2. **Wire events**  
   - Create EventBridge Pipes for `aws.config` ComplianceChange events and Security Hub findings; filter by severity/compliance.  
   - Use Step Functions with a Distributed Map state to orchestrate Lambda tasks for evidence generation and Vanta API calls.  
3. **Shape & submit evidence**  
   - Generate a compact evidence JSON (rule/finding, account, region, resource, proof link, timestamp).  
   - Upload the file to Vanta and submit it to the correct document; store returned IDs for traceability.  
4. **Make Vanta understand AWS signals**  
   - Create custom controls for bespoke requirements.  
   - Send custom resources representing your normalized AWS signal so Custom Tests evaluate pass/fail automatically.  
   - Query failing tests regularly; open tickets or notify owners; verify remediation.  
5. **Publish & govern**  
   - Export OSCAL assessment results and component definitions for auditors.  
   - (Optional) Surface safe control statuses in Vanta Trust Center; control what is visible to external stakeholders.  

## 🌟 Progressive Add‑Ons  
- **MCP “compliance copilot”** – integrate with Vanta MCP to allow AI assistants to answer queries like “what controls are failing & why?” (experimental).  
- **Evidence lineage** – attach the original OCSF record ARN in your evidence JSON for audit chain‑of‑custody.  
- **Org‑scale backfills** – use Step Functions Distributed Map for multi‑account backfills or periodic control re‑evidence.  

## 🔒 Notes & Gotchas  
- **Auth & rate limits** – use Vanta OAuth client credentials; respect API rate limits; rotate secrets via Secrets Manager.  
- **Trust Center APIs** – currently support listing and reading; treat publishing or visibility changes as governed operations.  
- **Test state writes** – Vanta calculates test results from the data you send; rely on custom resources and tests rather than manually setting pass/fail.  

## 📚 References  
- [Vanta API Documentation](https://developers.vanta.com/)  
- [AWS Config Conformance Packs](https://docs.aws.amazon.com/config/latest/developerguide/conformance-pack-template-creation.html)  
- [AWS Security Hub](https://aws.amazon.com/security-hub/)  
- [AWS Security Lake & OCSF](https://aws.amazon.com/security-lake/)  
- [NIST OSCAL](https://pages.nist.gov/OSCAL/)  

## 📔 License  
MIT License © 2025 Xavier Motley 
