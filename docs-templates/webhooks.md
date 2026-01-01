# Webhook Documentation

## Overview

External integration endpoints for n8n and other services.

---

## Endpoints

[Document webhooks as they're added]

---

## Template

When adding a new webhook, use this format:

### [Endpoint Name]

**URL**: `/api/webhooks/[path]`
**Method**: POST
**Auth**: [API Key / Signature / None]

#### Headers
```
x-api-key: [key]
Content-Type: application/json
```

#### Request Payload
```json
{
  "field": "type"
}
```

#### Response (200 Success)
```json
{
  "success": true,
  "data": {}
}
```

#### Response (400 Error)
```json
{
  "success": false,
  "error": "Error message",
  "code": "ERROR_CODE"
}
```

#### n8n Setup
- **Trigger**: Webhook node
- **Method**: POST
- **Path**: /api/webhooks/[path]
- **Auth**: [how to configure]

#### Example Use Case
[Brief description of typical workflow]
