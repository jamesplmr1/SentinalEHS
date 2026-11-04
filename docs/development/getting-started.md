## Local HTTPS

`SentinelEHS.Api` uses HTTPS for local development.

The local HTTP and HTTPS addresses are defined in:

`SentinelEHS.Api/Properties/launchSettings.json`

The assigned port numbers may differ between development environments.

When the API starts successfully, the console displays the addresses on which the application is listening.

Developers should use the HTTPS address when accessing API endpoints and OpenAPI documentation.

The generated OpenAPI document is available in the Development environment at:

`https://localhost:{https-port}/openapi/v1.json`

The project uses the ASP.NET Core development certificate for local HTTPS. This certificate is intended only for local development and must not be used as a production certificate.

No development certificate private keys, passwords, production certificates, or other secrets may be committed to the repository.

If the browser reports that the local HTTPS certificate is untrusted, the developer should verify or renew the ASP.NET Core development certificate using the official .NET development-certificate tooling.

Production HTTPS, TLS termination, certificate renewal, and Azure hostname configuration are outside the scope of local development setup and will be defined as part of the deployment architecture.
