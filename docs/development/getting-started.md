## Application Configuration

Sentinel EHS uses the standard ASP.NET Core configuration system.

Shared non-sensitive application defaults are stored in:

`SentinelEHS.Api/appsettings.json`

Environment-specific non-sensitive overrides may be stored in files following the standard ASP.NET Core convention:

`appsettings.{Environment}.json`

For local development, Sentinel currently uses:

`SentinelEHS.Api/appsettings.Development.json`

The local Visual Studio launch profile configures the application to run using the `Development` environment.

Configuration providers are layered. Environment-specific configuration can therefore override matching values from the base configuration.

### Configuration and Secrets

Committed application settings must contain only non-sensitive configuration.

The following must not be committed to `appsettings.json`, environment-specific settings files, or other repository files:

- Passwords
- API keys
- OAuth or application client secrets
- Access or refresh tokens
- Certificate private keys
- Production database credentials
- Storage access keys

Local development secrets will use an appropriate local secret mechanism.

Deployed environments will obtain secrets through approved environment configuration and secret-management services such as Azure Key Vault where appropriate.

### Environments

Sentinel EHS uses the standard ASP.NET Core environment model.

The expected environments are:

- `Development` for local developer execution.
- `Staging` for pre-production validation when provisioned.
- `Production` for the live application when provisioned.

Environment-specific files should only be introduced when an environment has genuine configuration differences. Empty or speculative environment files should not be created merely to mirror the environment list.

Application source code must not depend on developer names, workstation names, or other machine-specific identifiers to determine environment behaviour.
