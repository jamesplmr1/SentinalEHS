## Starting the API

### Prerequisites

Local development of the Sentinel EHS backend currently requires:

- Visual Studio with ASP.NET and web-development support.
- The .NET 10 SDK and development tooling.
- A trusted ASP.NET Core development HTTPS certificate.
- A local checkout of the Sentinel EHS repository.

Additional dependencies will be documented when they become required by the application.

### Startup Project

The backend application host is:

`SentinelEHS.Api`

When developing in Visual Studio, ensure `SentinelEHS.Api` is configured as the startup project.

The normal local launch profile runs the application using the ASP.NET Core `Development` environment.

### Starting from Visual Studio

To start the API:

1. Open the Sentinel EHS solution.
2. Confirm that `SentinelEHS.Api` is the startup project.
3. Select the HTTPS development launch profile.
4. Start the application using Visual Studio debugging or run without debugging.
5. Review the application startup output.

A successful startup reports that:

- The application is running.
- The hosting environment is `Development`.
- The application is listening on local HTTP and HTTPS addresses.

The assigned development ports are controlled by the local launch profile and must not be assumed to be identical between development environments.

## Accessing the API

Developers should use the HTTPS address reported by the running application.

The address will normally follow the form:

```text
https://localhost:{https-port}
```

The local port can be determined from:

- The application startup output.
- The active Visual Studio launch profile.
- `SentinelEHS.Api/Properties/launchSettings.json`

The port number must not be hard-coded into application logic or permanent developer documentation.

### API Root

The API does not currently guarantee a response at the root path:

```text
/
```

Receiving a `404 Not Found` from the root URL does not necessarily indicate that the application failed to start.

Use the OpenAPI document or a known API endpoint to verify the application.

## OpenAPI

In the `Development` environment, the generated OpenAPI document can be accessed at:

```text
https://localhost:{https-port}/openapi/v1.json
```

A successful response returns a JSON description of the currently implemented API.

OpenAPI is exposed at runtime in Development only by default.

Interactive API documentation such as Swagger UI or Scalar is not currently required by Sentinel EHS.

## Verifying the API

After starting `SentinelEHS.Api`, verify the local development environment as follows:

1. Confirm the startup output reports `Development` as the hosting environment.
2. Confirm an HTTPS listening address is displayed.
3. Open the OpenAPI endpoint using the reported HTTPS port.
4. Confirm that a valid JSON OpenAPI document is returned.
5. Where a development API endpoint exists, confirm that the endpoint can be called successfully over HTTPS.

A successful OpenAPI response confirms that the API host, HTTPS configuration, request pipeline, and OpenAPI generation are operational.

## Stopping the API

Stop the debugging session through Visual Studio when development or testing is complete.

Stopping the development host does not alter application configuration or persisted source files.

## Troubleshooting

### HTTPS Certificate Warning

If the browser reports that the localhost certificate is not trusted, verify the ASP.NET Core development certificate using the official .NET development-certificate tooling.

Development certificates are for local development only and must not be used as production certificates.

### OpenAPI Returns 404

Confirm that:

- The application is running.
- The hosting environment is `Development`.
- The correct HTTPS port is being used.
- The request path is `/openapi/v1.json`.

### Application Does Not Start

Review the Visual Studio output and application console for startup errors.

Check that:

- `SentinelEHS.Api` is the startup project.
- The required .NET SDK is installed.
- The configured development ports are available.
- The local application configuration does not contain invalid values.

Do not change application source code merely to work around workstation-specific configuration problems without first identifying the underlying cause.
