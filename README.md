# Inclusive Employment Portal

An ASP.NET Core employment portal connecting job seekers, employers, career advisors, and administrators. This recruiter-facing copy also includes an interview-scheduling service boundary implemented as a local HTTP host and an AWS Lambda handler, plus an Amazon S3 service for resume storage.

The repository demonstrates MVC application structure, EF Core persistence, ownership-aware workflows, cloud integration boundaries, input validation, and integration testing. It does not claim to be a deployed production service.

## What problem it addresses

The portal supports an inclusive employment workflow in which:

- job seekers browse vacancies, manage profiles, apply, and request career support;
- employers manage profiles, vacancies, applications, interviews, and inquiries;
- career advisors manage guidance, recommendations, and training programs; and
- administrators monitor users, employers, vacancies, announcements, and resources.

## Features represented in the source

- ASP.NET Core MVC with role-oriented controllers and Razor views
- Entity Framework Core models, SQL Server persistence, and migrations
- session-based login and role-aware workflows
- employer-owned vacancy, application, inquiry, and interview operations
- ownership checks that scope employer data to the authenticated employer
- resume upload and pre-signed download URL support through Amazon S3
- interview scheduling through a typed HTTP client and explicit DTO contracts
- local interview service boundary for integration testing
- AWS Lambda handler for the scheduling boundary
- xUnit-style service, contract, host, Lambda, and MVC integration tests

## Architecture

```text
Browser
  -> ASP.NET Core MVC
       -> Controllers / Razor Views
       -> EF Core / SQL Server
       -> S3Service / Amazon S3 for resume objects
       -> InterviewApiClient
            -> LocalApi during local verification
            -> or verified HTTPS API Gateway endpoint
                 -> AWS Lambda handler
                      -> InterviewSchedulingService
                           -> EF Core / SQL Server
```

The MVC application and the scheduling boundary share typed contracts and the domain scheduling service. The LocalApi is explicitly a local service-boundary proof; it is not presented as a production deployment. The Lambda project reads its database connection and caller key from runtime environment configuration rather than application files.

See [`docs/architecture.md`](docs/architecture.md) and [`docs/aws.md`](docs/aws.md).

## Technology stack

- .NET 10
- ASP.NET Core MVC
- Razor Views
- Entity Framework Core 10
- SQL Server / LocalDB for development and tests
- Bootstrap and jQuery validation assets
- Amazon S3 via `AWSSDK.S3`
- AWS Lambda and API Gateway event types
- xUnit-based test projects

## Configuration and security

The committed `DDAC/appsettings.json` contains only a trusted local LocalDB example and a placeholder S3 bucket name. It does not contain the original remote database credential.

For a real environment, supply configuration outside source control:

```powershell
$env:ConnectionStrings__DefaultConnection = "<private SQL Server connection string>"
$env:AWS__region = "us-east-1"
$env:AWS__bucket_name = "<private S3 bucket>"
$env:EmployerInterview__BaseUrl = "<trusted HTTPS API endpoint>"
$env:EmployerInterview__CallerKey = "<at least 32 random characters>"
$env:DDAC_INTERVIEWS_CALLER_KEY = "<at least 32 random characters>"
```

Use AWS's default credential chain, an IAM role, or a local AWS profile. Do not place access keys, database passwords, caller keys, uploaded resumes, or production configuration in the repository.

See [`docs/configuration.md`](docs/configuration.md) and [`docs/security.md`](docs/security.md).

## Local setup

Prerequisites:

- .NET 10 SDK
- SQL Server LocalDB or another development SQL Server instance
- an AWS profile or IAM role only when testing S3-backed resume operations

From the repository root:

```powershell
dotnet restore DDAC.slnx
dotnet build DDAC.slnx --configuration Release
```

For local MVC development, set a private connection string through user secrets or `ConnectionStrings__DefaultConnection`, then apply the existing EF Core migration using your normal local database workflow. The sanitized copy does not create or include a database.

The LocalApi requires a pre-existing local test database and a runtime caller key. It intentionally fails closed without those values. Follow [`DDAC.Employer.Interviews.LocalApi/README.md`](DDAC.Employer.Interviews.LocalApi/README.md) for the boundary contract.

## Testing

The test source includes:

- employer baseline protection
- scheduling service validation and ownership tests
- transport contract tests
- LocalApi host tests
- MVC-to-LocalApi integration tests
- Lambda handler tests

Build before testing:

```powershell
dotnet build DDAC.slnx --configuration Release
dotnet test tests/DDAC.Employer.Tests/DDAC.Employer.Tests.csproj --configuration Release --no-build
dotnet test tests/DDAC.Employer.Lambda.Tests/DDAC.Employer.Lambda.Tests.csproj --configuration Release --no-build
```

The source documentation describes the required LocalDB fixture and environment variables. No test result files are included in this public copy, and this README does not claim a new test run.

## Limitations

- The current LocalApi is a loopback verification host, not a general-purpose production service.
- The Lambda project contains the handler and packaging configuration, but no deployment is claimed here.
- LocalDB assumptions remain in the local integration-test path.
- Authentication is the project's existing session-based implementation; a production deployment would need a fuller identity, secret-management, audit, and operational-hardening review.
- S3 access requires an appropriately scoped IAM role or profile and a private bucket policy.
- Visual, load, cloud-deployment, and cross-machine testing are outside the included evidence.

## Repository layout

```text
DDAC/                              MVC application, models, controllers, views, migrations
DDAC.Employer.Interviews.Lambda/  AWS Lambda scheduling handler
DDAC.Employer.Interviews.LocalApi/Local loopback scheduling boundary
tests/                             service, contract, host, Lambda, and MVC integration tests
docs/                              recruiter-facing architecture and operational notes
```

## License and attribution

No license was inferred from the source package. Add an appropriate license before public publication if desired.
