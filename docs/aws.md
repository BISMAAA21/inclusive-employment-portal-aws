# AWS integration

## Amazon S3

`DDAC/Services/S3Service.cs` uploads resume files under user-scoped object keys and generates short-lived pre-signed download URLs. The bucket name and region are configuration values. AWS credentials are not stored in the repository.

Use an IAM role or profile with least-privilege access to the required bucket. Uploaded resumes are user-provided documents and should not be committed, copied into `wwwroot`, or used as test fixtures in a public repository.

## AWS Lambda

`DDAC.Employer.Interviews.Lambda/Function.cs` handles API Gateway HTTP API v2 requests for interview scheduling. It:

- validates a runtime caller key;
- accepts the employer identity only from a trusted authenticated header;
- validates the scheduling DTO;
- invokes the shared scheduling service;
- returns constrained success, validation, not-found, unauthorized, and internal-error responses; and
- avoids returning request bodies, credentials, connection details, or exception messages.

The Lambda project reads `ConnectionStrings__DefaultConnection` and `DDAC_INTERVIEWS_CALLER_KEY` from runtime environment configuration. The excluded `employer-interviews.zip` was a local deployment artifact and is not part of the recruiter-facing copy.

## Deployment status

The source demonstrates the Lambda boundary and AWS SDK integration. This repository does not claim a current deployment, API Gateway configuration, production database, or production IAM policy.
