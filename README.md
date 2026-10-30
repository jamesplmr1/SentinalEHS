# Sentinel EHS

Sentinel EHS is a multilingual, multi-site Environmental, Health & Safety management platform for managing workplace incidents, investigations, corrective actions and EHS performance across an organisation.

## Features

The initial release will provide:

- Incident reporting and management
- Incident investigation workflows
- Corrective action management
- Site and location management
- Role-based access control
- Audit history
- EHS dashboards and reporting
- Multilingual support

### Supported Languages

- English (en-GB)
- French (fr-FR)
- German (de-DE)
- Dutch (nl-NL)

## Technology Stack

### Backend
- .NET 8
- ASP.NET Core
- ASP.NET Core Web API
- Entity Framework Core
- SQL Server

### Frontend
- React
- TypeScript

### DevOps & Infrastructure
- Docker
- GitHub
- Azure DevOps
- Azure Pipelines
- Microsoft Azure

## Architecture

Sentinel EHS is being developed as a modular monolith with clear separation between domain logic, application services, infrastructure and the Web API.

The solution is organised into:

- `SentinelEhs.Domain` - Domain entities, value objects and business rules
- `SentinelEhs.Application` - Application use cases and orchestration
- `SentinelEhs.Infrastructure` - Data persistence and external service implementations
- `SentinelEhs.Api` - ASP.NET Core Web API
- `sentinel-ehs-web` - React/TypeScript frontend

## Project Structure

```text
sentinel-ehs/
├── src/
│   ├── SentinelEhs.Api/
│   ├── SentinelEhs.Application/
│   ├── SentinelEhs.Domain/
│   └── SentinelEhs.Infrastructure/
├── web/
│   └── sentinel-ehs-web/
├── tests/
│   ├── SentinelEhs.UnitTests/
│   └── SentinelEhs.IntegrationTests/
├── docker-compose.yml
└── SentinelEhs.sln
