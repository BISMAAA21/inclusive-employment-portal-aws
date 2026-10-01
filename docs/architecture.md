# Architecture overview

## Application layers

| Layer | Responsibility |
| --- | --- |
| MVC controllers | HTTP actions, session checks, ownership checks, validation, and view models |
| Razor views | Role-specific server-rendered workflows for employers, job seekers, advisors, and administrators |
| EF Core | Relational models, SQL Server access, and tracked migrations |
| Domain services | Interview scheduling rules and reusable employer workflow logic |
| S3 service | Resume object upload and short-lived pre-signed download URLs |
| Integration boundary | Typed HTTP contract between MVC and a trusted scheduling host |
| Lambda handler | API Gateway request validation and invocation of the scheduling service |

## Interview scheduling flow

1. An authenticated employer submits an interview request through the MVC application.
2. The MVC layer validates the request and sends a typed DTO through `InterviewApiClient`.
3. A caller key and server-asserted employer ID authenticate the trusted service request.
4. The scheduling service verifies that the application belongs to the employer's vacancy.
5. Business rules validate interview type, date, and on-site location.
6. Interview creation and eligible application shortlisting are committed in one EF Core save boundary.
7. The boundary returns a constrained response DTO without EF entities, exception details, or secrets.

The same service logic is hosted by the local verification API and the Lambda handler. The local host is intentionally loopback-only and is not a cloud deployment claim.

## Data ownership

Employer reads and mutations are scoped using the authenticated employer identity rather than trusting route or form IDs alone. The scheduling boundary repeats the trusted identity check before querying or changing application data.
