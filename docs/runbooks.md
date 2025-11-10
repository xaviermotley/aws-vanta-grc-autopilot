# Runbooks  
## Break-Glass Procedure  
In the event of a critical failure of the GRC pipeline, follow these steps:  
1. Disable EventBridge Pipes rules to stop processing new events.  
2. Identify the failing component (evidence_builder or vanta_client).  
3. Use AWS CloudWatch to view recent errors and logs.  
4. Manually submit evidence via Vanta UI if necessary.  
5. Re-enable the pipeline after resolving the issue.  

## Token Rotation  
Vanta API uses OAuth credentials stored in Secrets Manager. To rotate:  
1. Generate a new client secret in the Vanta console.  
2. Update the secret value in AWS Secrets Manager.  
3. Trigger a rotation of credentials for Lambda functions (evidence_builder and vanta_client).  
4. Test the pipeline to ensure new tokens are being used.  

## Retries and Error Handling  
The Step Functions state machine should implement retry logic with exponential backoff for transient errors (e.g., network issues or rate limits).  
- For AWS service calls, use the built-in retry policy in Step Functions.  
- For Vanta API calls, catch HTTP errors in Lambda and retry up to 3 times with delays.  
- Record failed events to a dead-letter queue or S3 for manual follow-up. 
