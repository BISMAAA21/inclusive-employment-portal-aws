# Testing guide

The repository includes source for service, contract, host, Lambda, and MVC integration tests. The sanitized copy intentionally excludes historical `.trx` result files so that generated local evidence is not mistaken for a fresh run.

## Build

```powershell
dotnet build DDAC.slnx --configuration Release
```

## Test projects

```powershell
dotnet test tests/DDAC.Employer.Tests/DDAC.Employer.Tests.csproj --configuration Release --no-build
dotnet test tests/DDAC.Employer.Lambda.Tests/DDAC.Employer.Lambda.Tests.csproj --configuration Release --no-build
```

The LocalApi and MVC integration tests require the documented LocalDB database and runtime caller key. They use fictional local fixtures and should be run in an isolated development database. No cloud deployment or load test is implied.
