# promocode settings

JSON-shaped reference for the `promocode settings` object.

Notes:
- This file intentionally mirrors the API response structure.
- It uses `jsonc` style so descriptions can live next to fields.
- This object appears to describe promocode lifecycle, discount value, usage limits, eligibility, visibility, and audit history.

```jsonc
{
  "_id": {
    "type": "string",
    "meaning": "Internal promocode identifier."
  },
  "value": {
    "type": "string",
    "meaning": "Promocode text entered by the customer."
  },
  "endDate": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "Public or effective end date of the promocode validity period."
  },
  "dbName": {
    "type": "string",
    "meaning": "Technical tenant or database identifier."
  },
  "startDate": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "Public or effective start date of the promocode validity period."
  },
  "amount": {
    "type": "number",
    "meaning": "Discount amount or discount value in the system's stored minor units or internal format. Interpretation depends on `type`."
  },
  "archived": {
    "type": "boolean",
    "meaning": "Whether the promocode has been archived."
  },
  "authRequired": {
    "type": "boolean",
    "meaning": "Whether customer authentication is required to use the promocode."
  },
  "createdAt": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "Promocode creation timestamp."
  },
  "deleted": {
    "type": "boolean",
    "meaning": "Whether the promocode is marked as deleted."
  },
  "history": {
    "type": "array",
    "meaning": "Audit history of promocode changes and actions.",
    "itemShape": {
      "action": {
        "type": "string",
        "meaning": "History action type, for example `created`, and possibly updated/archived/paused-related actions."
      },
      "changes": {
        "type": "object",
        "meaning": "Structured diff or change payload for the history event. Can be empty."
      },
      "createdAt": {
        "type": "string",
        "format": "ISO datetime",
        "meaning": "Timestamp of the history event."
      },
      "user": {
        "type": "object",
        "meaning": "Actor who performed the action.",
        "shape": {
          "_id": {
            "type": "string",
            "meaning": "Internal user identifier."
          },
          "name": {
            "type": "string",
            "meaning": "Display name or email-role label of the actor."
          }
        }
      }
    }
  },
  "internalEndDate": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "Internal end date of promocode validity. May be used by back-office or normalized internal logic."
  },
  "internalStartDate": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "Internal start date of promocode validity. May be used by back-office or normalized internal logic."
  },
  "maxUsages": {
    "type": "number | null",
    "meaning": "Maximum total number of promocode usages allowed."
  },
  "maxUsagesPerUser": {
    "type": "number | null",
    "meaning": "Maximum number of usages allowed per customer/user."
  },
  "minOrderAmount": {
    "type": "number",
    "meaning": "Minimum order amount required for the promocode to apply."
  },
  "note": {
    "type": "string",
    "meaning": "Internal note for staff or operators. Not necessarily customer-facing."
  },
  "orderDiscountCompatibility": {
    "type": "boolean",
    "meaning": "Whether the promocode can be combined with other order-level discounts."
  },
  "orderTypes": {
    "type": "array",
    "meaning": "Order channels where the promocode can be used.",
    "itemShape": {
      "type": "string",
      "meaning": "Order type value such as `delivery`, `takeaway`, and possibly other channel names."
    }
  },
  "paused": {
    "type": "boolean",
    "meaning": "Whether promocode usage is temporarily paused."
  },
  "type": {
    "type": "string",
    "meaning": "Promocode discount type, likely enum-like values such as `amount`, and possibly percentage-based types."
  },
  "updatedAt": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "Promocode update timestamp."
  },
  "usageLimitReached": {
    "type": "boolean",
    "meaning": "Whether the promocode has already reached its allowed usage limit."
  },
  "visibility": {
    "type": "string",
    "meaning": "Promocode visibility mode, likely enum-like values such as `public`, and possibly private/internal variants."
  },
  "completed": {
    "type": "boolean",
    "meaning": "Lifecycle completion flag. Most likely indicates whether the promocode campaign or rule is considered finished."
  }
}
```

