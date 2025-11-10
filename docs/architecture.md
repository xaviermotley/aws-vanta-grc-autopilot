# Architecture  

This document describes the event-driven, serverless pipeline for AWS \u2194 Vanta GRC Autopilot.  

```mermaid  
graph TD  
    A[AWS Config & Security Hub & Security Lake] --> B[EventBridge Pipes]  
    B --> C[Step Functions]  
    C --> D[Evidence Builder Lambda]  
    D --> E[Vanta Client Lambda]  
    E --> F[Vanta API & Custom Controls]  
    E --> G[S3 Storage]  
    F --> H[Trust Center (optional)]  
    F --> I[OSCAL Export]  
```  

### Event Flow Description  

1. Compliance events from AWS Config, Security Hub, and optional Security Lake are published to EventBridge.  
2. EventBridge Pipes filter and enrich these events before invoking a Step Functions state machine.  
3. The state machine coordinates Lambda tasks: the evidence builder creates a JSON/PDF artifact summarizing the event and stores it in S3; then the Vanta client uploads the evidence, manages custom controls/resources, and updates status via the Vanta API.  
4. Optionally, the pipeline updates a Trust Center board and exports OSCAL artifacts for auditors. 
