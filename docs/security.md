# Security notes

The original project contained a hard-coded remote SQL Server username and password in `DDAC/appsettings.json`. That value has been removed from this sanitized copy and replaced with a trusted local LocalDB example. The original source remains untouched.

The public copy also excludes:

- uploaded resume PDFs;
- test result files;
- deployment ZIP files;
- `.git` worktree metadata;
- build output and temporary directories;
- production configuration and credentials.

Before publishing, supply real database strings, S3 bucket names, AWS credentials, and caller keys through runtime configuration only. If the original database credential was ever used outside the local environment, rotate it before any publication.
