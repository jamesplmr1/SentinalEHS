# Sentinel EHS API Route and Controller Conventions

## 1. Purpose

This document defines the HTTP API routing, controller, contract, serialization, response, and security-boundary conventions for Sentinel EHS.

The objective is to provide a consistent API contract that:

- Is predictable for frontend and integration clients.
- Keeps HTTP concerns separate from application and domain logic.
- Supports Sentinel EHS's multi-tenant security model.
- Produces consistent HTTP responses and errors.
- Can evolve without unnecessarily coupling the external API contract to internal C# implementation details.

These conventions apply to endpoints exposed by `SentinelEHS.Api`.

---

## 2. Base API Route

Application API endpoints will be exposed beneath:

```text
/api
```

Examples:

```text
/api/incidents
/api/hazards
/api/sites
/api/inspections
/api/corrective-actions
```

The `/api` prefix distinguishes HTTP API endpoints from other application endpoints, such as frontend resources, health checks, and OpenAPI documentation.

---

## 3. Resource Naming

API resource routes will use:

- Plural resource names.
- Lowercase characters.
- Kebab-case for multiword resource names.
- Nouns rather than operation names.

### Preferred

```text
/api/incidents
/api/hazards
/api/sites
/api/corrective-actions
/api/inspection-templates
```

### Avoid

```text
/api/Incident
/api/CorrectiveActions
/api/correctiveActions
/api/getIncidents
/api/createIncident
```

HTTP methods should communicate standard operations rather than embedding operation names into routes.

---

## 4. Explicit Routes

Sentinel EHS will use explicit resource routes rather than deriving its public API contract solely from controller class names.

For example, an incidents controller represents the route:

```text
/api/incidents
```

The external HTTP contract should remain stable if the internal C# class is subsequently renamed or reorganised.

The template convention:

```text
[controller]
```

must therefore not be treated as the routing convention for production Sentinel EHS endpoints.

---

## 5. Controller Organisation

Controllers will represent cohesive business resources or API capabilities.

Expected examples include:

```text
SitesController
IncidentsController
HazardsController
CorrectiveActionsController
InspectionsController
UsersController
```

Generic controllers such as the following should be avoided:

```text
EhsController
DataController
GeneralController
ManagementController
```

A controller should remain small enough for its responsibility to be clear.

---

## 6. Controller Responsibilities

Sentinel EHS will use **thin controllers**.

A controller is responsible for:

1. Receiving the HTTP request.
2. Interpreting HTTP-specific input.
3. Delegating the requested operation to the Application layer.
4. Translating the application result into an appropriate HTTP response.

The intended flow is:

```text
HTTP Request
     ↓
Controller
     ↓
SentinelEHS.Application
     ↓
SentinelEHS.Domain
     ↑
SentinelEHS.Infrastructure
     ↓
Application Result
     ↓
Controller
     ↓
HTTP Response
```

Controllers must not become the primary location for:

- Business rules.
- Entity Framework Core queries.
- SQL access.
- Tenant filtering.
- Effective-permission calculation.
- Complex application or domain validation.
- Notification processing.
- External service integration.

Those responsibilities belong in the appropriate Application, Domain, or Infrastructure components.

---

## 7. Standard Resource Operations

Standard resource operations will use standard HTTP methods.

For an `incidents` resource:

```text
GET     /api/incidents
GET     /api/incidents/{id}
POST    /api/incidents
PUT     /api/incidents/{id}
PATCH   /api/incidents/{id}
DELETE  /api/incidents/{id}
```

General semantics:

| HTTP method | Purpose |
|---|---|
| `GET` | Retrieve resources |
| `POST` | Create a resource or invoke a defined business operation |
| `PUT` | Replace or update a resource according to the endpoint contract |
| `PATCH` | Apply a partial modification |
| `DELETE` | Delete a resource where deletion is permitted |

Routes such as `/getIncidents` and `/updateIncident` should not be used where the HTTP method already expresses the operation.

---

## 8. Business Workflow Operations

Not every Sentinel EHS operation is CRUD.

Meaningful domain transitions may be represented explicitly when an operation has business semantics beyond changing a property.

Examples could include:

```text
POST /api/incidents/{id}/submit
POST /api/incidents/{id}/close
POST /api/incidents/{id}/reopen
```

For example, closing an incident may eventually require:

- Appropriate permission.
- Access to the incident through the current record scope.
- Completion of mandatory investigation information.
- Completion or disposition of required corrective actions.
- Creation of audit records.

In such cases, `close` represents a business operation rather than merely changing a `Status` property.

Explicit action routes should be used selectively and must not replace normal resource-oriented HTTP operations.

---

## 9. Multi-Tenant Routing

Normal tenant-owned resources will not require a tenant identifier to be embedded throughout the API route structure.

For example, prefer:

```text
GET /api/incidents
```

rather than making the standard pattern:

```text
GET /api/tenants/{tenantId}/incidents
```

Tenant access will ultimately be determined through trusted server-side context:

```text
Authenticated Identity
        ↓
Tenant Membership
        ↓
Validated Tenant Context
        ↓
Effective Permissions
        ↓
Record and Site Scope
        ↓
Requested Operation
```

A tenant identifier provided by a client must never, by itself, constitute evidence that the client is permitted to access that tenant.

Tenant isolation remains a mandatory server-side security boundary regardless of route design.

---

## 10. API Request and Response Contracts

Domain entities will not be exposed directly as the public HTTP contract.

Sentinel EHS will use explicit request and response models appropriate to each API operation.

Conceptual examples include:

```text
CreateIncidentRequest
UpdateIncidentRequest
IncidentResponse
IncidentSummaryResponse
```

This maintains separation between:

```text
HTTP Contract
      ≠
Application Model
      ≠
Domain Model
      ≠
Persistence Model
```

This allows the internal domain and persistence architecture to evolve without automatically introducing breaking changes into the external API.

---

## 11. JSON Serialization

Sentinel EHS will use a consistent JSON representation across all API endpoints.

JSON serialization conventions form part of the public API contract and must remain predictable for frontend and integration clients.

### 11.1 Property Naming

JSON property names will use **camelCase**.

C# types may continue to use standard PascalCase naming conventions. The JSON representation will use the equivalent camelCase property name.

Examples:

| C# property | JSON property |
|---|---|
| `IncidentId` | `incidentId` |
| `ReportedDate` | `reportedDate` |
| `PotentialSeverity` | `potentialSeverity` |
| `CorrectiveActions` | `correctiveActions` |

This convention aligns the API contract with the standard JavaScript and TypeScript naming conventions used by the Sentinel EHS React frontend.

### 11.2 Enumeration Values

Enumerations exposed through the API will use meaningful string representations rather than numeric ordinal values.

For example, an incident status should be represented as:

```json
{
  "status": "underInvestigation"
}
```

It should not be represented as:

```json
{
  "status": 2
}
```

API clients must not be coupled to the internal numeric ordering of C# enumeration values.

Enumeration values that form part of the API contract should remain stable after publication.

### 11.3 Dates and Times

Dates and timestamps will use culture-independent, ISO 8601-compatible representations.

A date may be represented as:

```json
{
  "reportedDate": "2026-11-04"
}
```

A timestamp must retain sufficient UTC or offset information to identify the represented instant unambiguously.

Formatting dates for a user's language or regional preference is a presentation concern and will normally be handled by the frontend.

The underlying API representation must not vary according to the server or user's regional settings.

### 11.4 Null Values

Null properties will be retained by default when a property forms part of an API contract but currently has no value.

For example:

```json
{
  "closedDate": null
}
```

This explicitly communicates that `closedDate` is a recognised part of the contract but has not yet been populated.

Properties may be deliberately omitted where a particular API contract requires different semantics. Null values will not be globally suppressed merely to reduce response size.

### 11.5 Explicit API Contracts

JSON serialization will operate on deliberately designed API request and response contracts.

Sentinel EHS will not expose Domain or persistence entities directly for serialization.

The intended boundary remains:

```text
Domain Model
     ↓
Application Result
     ↓
API Response Contract
     ↓
JSON Serialization
     ↓
HTTP Response
```

This prevents persistence relationships and internal implementation details from unintentionally becoming part of the external API contract.

### 11.6 Circular References

Circular object-reference problems must be addressed through appropriate API contract design rather than by globally configuring the serializer to ignore reference loops.

For example, internal relationships may conceptually include:

```text
Site
 └── Incidents
      └── Site
           └── Incidents
```

An API response must not expose an unrestricted representation of this object graph.

Instead, endpoints should return deliberately shaped representations such as:

```text
SiteSummaryResponse
IncidentSummaryResponse
IncidentDetailResponse
```

Global reference-loop suppression must not be used to compensate for inappropriate API response models.

### 11.7 Response Shape

API responses should contain the information required by the relevant use case rather than automatically serializing complete internal object graphs.

List and detail endpoints may therefore expose different response contracts.

For example:

```text
GET /api/incidents
        ↓
IncidentSummaryResponse

GET /api/incidents/{id}
        ↓
IncidentDetailResponse
```

This allows Sentinel EHS to:

- Control response size.
- Avoid unintended information disclosure.
- Provide clients with purpose-specific representations.
- Evolve internal models independently of the HTTP contract.

### 11.8 Request Validation

JSON serialization and request validation are separate concerns.

Successfully deserializing JSON does not imply that a request represents a valid Sentinel EHS operation.

Request validity will be determined through the appropriate:

- API validation.
- Application validation.
- Domain rules.
- Authentication and authorisation checks.
- Tenant-membership checks.
- Record and site scope checks.

### 11.9 Configuration Principle

Sentinel EHS will retain standard ASP.NET Core JSON behaviour where that behaviour already satisfies these conventions.

Explicit serializer configuration will only be introduced where required to enforce an agreed API behaviour.

Serializer configuration must not be added solely to restate framework defaults.

### 11.10 JSON Convention Summary

The Sentinel EHS JSON contract uses:

- camelCase property names.
- Meaningful string representations for API-facing enumeration values.
- Culture-independent ISO 8601-compatible dates and timestamps.
- Null properties retained by default.
- Explicit API request and response contracts.
- Deliberately shaped response models.
- No direct serialization of Domain or persistence entities.
- No global circular-reference suppression as a substitute for correct API design.
- Explicit validation independently of JSON deserialization.

These conventions apply consistently across Sentinel EHS API endpoints.

---

## 12. HTTP Status Codes

Sentinel EHS will use standard HTTP status-code semantics consistently.

The principal responses will include:

| Status | Meaning |
|---|---|
| `200 OK` | The request was successfully processed and a response is returned |
| `201 Created` | A new resource was successfully created |
| `204 No Content` | The request succeeded and no response body is required |
| `400 Bad Request` | The request is malformed or invalid at the HTTP/API boundary |
| `401 Unauthorized` | Authentication is missing or invalid |
| `403 Forbidden` | The caller is authenticated but does not have permission |
| `404 Not Found` | The requested resource is unavailable to the caller |
| `409 Conflict` | The request conflicts with the resource's current state |
| `500 Internal Server Error` | An unexpected server-side error occurred |

Additional status codes may be introduced where their semantics genuinely improve the API contract.

Security-sensitive resource lookups must avoid unnecessarily disclosing the existence of records that the caller cannot access.

---

## 13. Error Responses

API failures will use a consistent **Problem Details** representation.

Sentinel EHS must not introduce different bespoke error structures for individual controllers.

Problem responses should provide sufficient information for legitimate clients to understand and handle a failure while avoiding disclosure of:

- Stack traces.
- Internal implementation details.
- Database information.
- Sensitive security details.
- Information about inaccessible tenant records.

The detailed implementation of the error-handling mechanism is handled separately by the API error-handling work.

---

## 14. Authentication and Authorisation

Frontend behaviour is not a security boundary.

Every protected API operation must ultimately enforce the appropriate authentication, tenant, permission, and record-access requirements on the server.

The conceptual authorisation sequence is:

```text
Authentication
      ↓
Tenant Context
      ↓
Tenant Membership
      ↓
Effective Permission
      ↓
Record and Site Scope
      ↓
Application Operation
```

A user possessing a URL or resource identifier does not imply permission to access the corresponding resource.

---

## 15. API Versioning

Sentinel EHS will not introduce explicit API versioning initially.

Initial routes will therefore use:

```text
/api/incidents
```

rather than:

```text
/api/v1/incidents
```

Versioning will be reconsidered when Sentinel EHS requires simultaneous support for multiple API contracts, particularly if external systems or customers become independently versioned consumers of the API.

The absence of versioning in the initial implementation does not preclude introducing an API-versioning strategy later.

---

## 16. Convention Summary

Sentinel EHS will use:

```text
Explicit API routes
        ↓
/api/{resource}
        ↓
Plural lowercase kebab-case resources
        ↓
Cohesive thin controllers
        ↓
Explicit request and response contracts
        ↓
Consistent JSON serialization
        ↓
Application-layer delegation
        ↓
Domain behaviour
```

Security remains external to the route itself:

```text
Request
   ↓
Authentication
   ↓
Tenant Isolation
   ↓
Configurable Authorisation
   ↓
Record Scope
   ↓
Controller
   ↓
Application
```

These conventions establish the initial API design standard for Sentinel EHS.

Conventions may evolve where implementation experience identifies a genuine requirement, but deviations must be deliberate, documented, and applied consistently.
