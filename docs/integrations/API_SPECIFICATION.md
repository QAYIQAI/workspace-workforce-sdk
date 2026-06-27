# API Specification

Workspace Workforce gebruikt REST API’s onder /api/v1.

## Conventies

```text
GET    /api/v1/employees
POST   /api/v1/employees
GET    /api/v1/employees/:id
PATCH  /api/v1/employees/:id
DELETE /api/v1/employees/:id
```

## Response

```json
{
  "success": true,
  "data": {},
  "meta": {}
}
```

## Error

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "De invoer is ongeldig.",
    "details": []
  }
}
```

## Auth

Elke API-route vereist authenticatie, behalve expliciet publieke health-checks.

## Rechten

Elke API-route controleert rechten server-side.

## Idempotency

Kritieke POST-routes ondersteunen idempotency keys om dubbele verwerking te voorkomen.
