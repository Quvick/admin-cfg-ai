# pos

JSON-shaped reference for the `pos` response object.

Notes:
- This file intentionally mirrors the API response structure.
- It uses `jsonc` style so descriptions can live next to fields.
- The provided object looks like an error response from POS-related API/integration, not a successful POS configuration payload.

```jsonc
{
  "error": {
    "type": "boolean",
    "meaning": "Indicates whether the response represents an error state."
  },
  "message": {
    "type": "string",
    "meaning": "Machine-readable or localization-ready error message key, for example `user.notFound`."
  },
  "name": {
    "type": "string",
    "meaning": "Error class or error type name, for example `NonCriticalError`."
  },
  "statusCode": {
    "type": "number",
    "meaning": "HTTP-like status code associated with the error, for example `401` for unauthorized access."
  }
}
```

