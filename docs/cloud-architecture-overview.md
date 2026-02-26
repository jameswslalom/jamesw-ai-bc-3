# Cloud Architecture Overview

This document provides a simple system context view for the TODO app monorepo.

## System Context

```mermaid
flowchart LR
    U[End User]
    B[Web Browser]

    subgraph Cloud[Cloud Runtime]
      FE[Frontend App\nReact (packages/frontend)]
      API[Backend API\nExpress (packages/backend)]
      DB[(Task Data Store\nSQLite in-memory)]
    end

    U --> B
    B -->|HTTPS| FE
    FE -->|HTTP /api/tasks| API
    API --> DB
```

## Component Notes

- Frontend: React single-page app served to browser clients.
- Backend: Express API exposing task endpoints under `/api/tasks`.
- Data store: SQLite in-memory database used by backend process.
- Current scope: single-user/local-style persistence behavior as defined in project requirements.

## Future-State Deployment View

```mermaid
flowchart LR
  U[End User]
  DNS[DNS]
  CDN[CDN / Edge]
  FEH[Frontend Hosting\nStatic React Build]
  APIGW[API Gateway / Ingress]
  APISVC[Backend Service\nExpress API]
  DB[(Managed SQL Database)]
  OBS[Monitoring & Logs]

  U --> DNS --> CDN --> FEH
  FEH -->|HTTPS /api/tasks| APIGW --> APISVC --> DB
  APISVC --> OBS
  APIGW --> OBS
```

### Future-State Notes

- Frontend is hosted as static assets behind CDN/edge caching.
- API traffic is routed through an ingress layer before reaching backend service instances.
- Database moves from in-memory SQLite to a managed persistent SQL service.
- Monitoring and logs are centralized for API and gateway telemetry.

## Sequence: Create TODO

```mermaid
sequenceDiagram
  actor User
  participant Browser
  participant Frontend as React Frontend
  participant API as Express API
  participant DB as Task Data Store

  User->>Browser: Enter title/description/due date
  Browser->>Frontend: Submit "Add Task" form
  Frontend->>Frontend: Validate required title

  alt Validation fails
    Frontend-->>Browser: Show validation error
    Browser-->>User: Display error message
  else Validation passes
    Frontend->>API: POST /api/tasks (task payload)
    API->>API: Validate request body

    alt API validation fails
      API-->>Frontend: 400 Bad Request
      Frontend-->>Browser: Render API error
      Browser-->>User: Display error message
    else API validation passes
      API->>DB: Insert task record
      DB-->>API: Return new task id/record
      API-->>Frontend: 201 Created (new task)
      Frontend->>API: GET /api/tasks
      API->>DB: Query tasks
      DB-->>API: Return task list
      API-->>Frontend: 200 OK (task list)
      Frontend-->>Browser: Re-render list with new TODO
      Browser-->>User: Show created TODO
    end
  end
```
