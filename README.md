# Sentinel EHS

Sentinel EHS is a multilingual, multi-tenant Environmental, Health & Safety management platform for managing workplace incidents, hazards, investigations, corrective actions, inspections and EHS performance across multiple organisations and operational sites.

The project is being developed as a production-style SaaS application using modern .NET, React, SQL and Azure technologies.

## Core Capabilities

The planned platform includes:

- Incident reporting and management
- Incident investigation workflows
- Hazard and near-miss reporting
- Corrective and preventive action management
- Inspection and audit management
- Multi-tenant organisation management
- Multi-site and location management
- Configurable roles, permission sets and access controls
- Tenant and site-scoped data access
- Audit history
- EHS dashboards and reporting
- Document and evidence management
- Notifications and scheduled processing
- Multilingual user experience

## Supported Languages

The initial language targets are:

- English (`en-GB`)
- French (`fr-FR`)
- German (`de-DE`)
- Dutch (`nl-NL`)

## Technology Stack

### Backend

- .NET 10 LTS
- ASP.NET Core
- ASP.NET Core Web API
- Entity Framework Core
- SQL Server / Azure SQL
- Microsoft Entra ID

### Frontend

Planned frontend:

- React
- TypeScript

### DevOps and Infrastructure

The planned delivery platform includes:

- GitHub for source control and pull requests
- Azure DevOps Boards for backlog and work-item management
- Azure Pipelines for CI/CD
- Docker for containerised development and deployment
- Microsoft Azure for application hosting and managed services

## Architecture

Sentinel EHS is being developed as a modular monolith with clear separation between domain logic, application use cases, infrastructure concerns and the HTTP API.

The backend solution is organised into:

- `SentinelEHS.Domain` - Domain entities, value objects and business rules
- `SentinelEHS.Application` - Application use cases and orchestration
- `SentinelEHS.Infrastructure` - Persistence and external service implementations
- `SentinelEHS.Api` - ASP.NET Core Web API and application host

The intended dependency direction keeps business logic independent of infrastructure and HTTP concerns.

```text
SentinelEHS.Api
        │
        ├──────────────┐
        ▼              ▼
SentinelEHS.Application
        │       SentinelEHS.Infrastructure
        ▼              │
SentinelEHS.Domain ◄────┘
