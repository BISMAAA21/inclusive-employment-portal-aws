# Configuration guide

The public copy contains safe local examples only. Keep real values in user secrets, environment variables, an approved secret manager, or an IAM role.

## MVC application

Override the connection string with the standard .NET environment-variable form:

```powershell
$env:ConnectionStrings__DefaultConnection = "<private SQL Server connection string>"
$env:AWS__region = "us-east-1"
$env:AWS__bucket_name = "<private S3 bucket>"
$env:EmployerInterview__BaseUrl = "http://127.0.0.1:5244"
$env:EmployerInterview__CallerKey = "<at least 32 random characters>"
```

`EmployerInterview__BaseUrl` and `EmployerInterview__CallerKey` are read by `Program.cs`. The caller key must remain server-side and must never be sent to browser JavaScript.

## LocalApi

```powershell
$env:DDAC_INTERVIEWS_CALLER_KEY = "<at least 32 random characters>"
$env:DDAC_INTERVIEWS_PORT = "5244"
```

The LocalApi uses its own fixed LocalDB test target and verifies the database before listening. It does not load the MVC application's settings or cloud credentials.

## Lambda

Configure these values through the Lambda runtime environment or an attached secret/configuration service:

```text
ConnectionStrings__DefaultConnection=<private SQL Server connection string>
DDAC_INTERVIEWS_CALLER_KEY=<at least 32 random characters>
```

AWS SDK credentials should come from the Lambda execution role or the standard AWS credential chain, never from committed files.
