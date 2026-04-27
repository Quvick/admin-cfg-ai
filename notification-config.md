# notification config

JSON-shaped reference for the `notification config` object.

Notes:
- This file intentionally mirrors the API response structure.
- It uses `jsonc` style so descriptions can live next to fields.
- This object appears to control notification activation, feature-specific configuration, transport configuration, and versioning.

```jsonc
{
  "_id": {
    "type": "string",
    "meaning": "Internal notification configuration identifier."
  },
  "dbName": {
    "type": "string",
    "meaning": "Technical tenant or database identifier."
  },
  "active": {
    "type": "boolean",
    "meaning": "Global active state of the notification configuration."
  },
  "featureConfig": {
    "type": "object",
    "meaning": "Feature-specific notification settings. Can be empty if no feature-level overrides are configured."
  },
  "transportConfig": {
    "type": "object",
    "meaning": "Notification delivery transport settings.",
    "shape": {
      "telegram": {
        "type": "object",
        "meaning": "Telegram transport configuration.",
        "shape": {
          "link": {
            "type": "string",
            "meaning": "Telegram bot onboarding or connection link."
          },
          "code": {
            "type": "string",
            "meaning": "Telegram connection or activation code."
          }
        }
      }
    }
  },
  "version": {
    "type": "number",
    "meaning": "Configuration schema or object version number."
  }
}
```

