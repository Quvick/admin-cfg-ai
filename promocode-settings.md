# promocode settings

JSON-shaped reference for the `promocode settings` object (a single promocode item).

Notes:

- This file intentionally mirrors the API response structure.
- It uses `jsonc` style so descriptions can live next to fields.
- This object describes a single promocode: lifecycle, discount value, usage limits, eligibility, visibility, and audit history.
- Editor lives at `/loyalty-program/promocodes/<id>` (or `/new`). Shown only to users with the `promocodes` company feature.
- Money fields are stored in minor units. The form scales `amount` and `minOrderAmount` by `AMOUNT_MULTIPLY` on save and divides by it on load — except when `type` is `percent`, where `amount` is stored as-is (the raw percentage value).
- For chains, save calls a chain-aware endpoint and the venue selection is edited in a separate `Venues` block.
- Validations enforced before save:
  - `value`, `amount`, and at least one of `orderTypes` are required.
  - When both `maxUsages` and `maxUsagesPerUser` are set, `maxUsagesPerUser <= maxUsages`.
- Lifecycle actions exposed in the editor: pause/release, complete, archive, delete, restore.

```jsonc
{
  "_id": {
    "type": "string",
    "behavior": "Server-managed.",
    "meaning": "Internal promocode identifier."
  },
  "value": {
    "type": "string",
    "behavior": "Required. The text customers enter at checkout. Whitespace is collapsed and trimmed on input. Disabled in the editor once the promocode has been created (cannot be renamed).",
    "meaning": "Promocode text entered by the customer."
  },
  "type": {
    "type": "string",
    "values": ["percent", "amount"],
    "default": "percent",
    "behavior": "Discount mode. `percent` stores `amount` as a raw percentage value; `amount` stores `amount` in minor units (multiplied by AMOUNT_MULTIPLY on save, divided on load). The radio is disabled after creation (cannot change discount mode of an existing code).",
    "meaning": "Promocode discount type."
  },
  "amount": {
    "type": "number",
    "behavior": "Required. Discount value. Interpretation depends on `type`: percent → raw % value; amount → currency in minor units (scaled by AMOUNT_MULTIPLY on save). Disabled in the editor once the promocode has been created.",
    "meaning": "Discount amount or percentage. Interpretation depends on `type`."
  },
  "minOrderAmount": {
    "type": "number",
    "behavior": "Always stored in minor units (scaled by AMOUNT_MULTIPLY on save, divided on load).",
    "meaning": "Minimum order amount required for the promocode to apply."
  },
  "maxUsages": {
    "type": "number | null",
    "default": 0,
    "behavior": "Total usage limit across all customers. UI exposes a tumbler that switches between unlimited (null) and a numeric input. Validated against `maxUsagesPerUser` on save.",
    "meaning": "Maximum total number of promocode usages allowed. null = unlimited."
  },
  "maxUsagesPerUser": {
    "type": "number | null",
    "default": 1,
    "behavior": "Per-customer usage cap. Only editable while `authRequired` is true (the whole block is hidden otherwise). Tumbler switches between a fixed limit and 'unlimited' (null). On save, must be `<= maxUsages` when both are set.",
    "meaning": "Maximum number of usages allowed per customer."
  },
  "usages": {
    "type": "number | null",
    "behavior": "Server-managed counter of how many times the promocode has been used.",
    "meaning": "Current usage counter."
  },
  "usageLimitReached": {
    "type": "boolean",
    "behavior": "Server-derived flag that mirrors whether `usages` has reached `maxUsages`.",
    "meaning": "Whether the promocode has reached its allowed usage limit."
  },
  "authRequired": {
    "type": "boolean",
    "default": false,
    "behavior": "When true, the per-user limit and verification block are revealed in the editor. Required to be true for `verificationType` and `maxUsagesPerUser` to take effect.",
    "meaning": "Whether customer authentication is required to use the promocode."
  },
  "smsVerificationRequired": {
    "type": "boolean",
    "default": false,
    "behavior": "Legacy/parallel flag for SMS verification at use time.",
    "meaning": "Whether SMS verification is required at promocode use."
  },
  "verificationType": {
    "type": "string",
    "values": ["accountLogin", "phoneVerification"],
    "default": "accountLogin",
    "behavior": "Only editable while `authRequired` is true. When set to `phoneVerification`, the `table` and `pos` order types are forcibly removed from `orderTypes` and disabled in the order-type checkboxes (those channels do not support phone verification).",
    "meaning": "How the customer must verify their identity."
  },
  "orderTypes": {
    "type": "array",
    "default": ["delivery", "takeaway", "table", "pos"],
    "constraint": "non-empty",
    "behavior": "Required (at least one). When `verificationType` is `phoneVerification`, only `delivery` and `takeaway` are allowed; `table` and `pos` are disabled in the form and stripped from the value.",
    "itemShape": {
      "type": "string",
      "values": ["delivery", "takeaway", "table", "pos"],
      "meaning": "Order channel."
    }
  },
  "orderDiscountCompatibility": {
    "type": "boolean",
    "default": true,
    "behavior": "Toggle in the editor's compatibility section. When true, the promocode can stack with order-level discounts.",
    "meaning": "Whether the promocode can be combined with other order-level discounts."
  },
  "startDate": {
    "type": "string",
    "format": "ISO datetime",
    "behavior": "Optional. Date picker becomes disabled once the start date is in the past. Removed from the payload when empty.",
    "meaning": "Effective start date of the promocode validity period."
  },
  "endDate": {
    "type": "string",
    "format": "ISO datetime",
    "behavior": "Optional. Visually marked as an error when set before `startDate`. Removed from the payload when empty.",
    "meaning": "Effective end date of the promocode validity period."
  },
  "internalStartDate": {
    "type": "string",
    "format": "ISO datetime",
    "behavior": "Server-managed. Internal/normalised start date used by back-office logic (may differ from `startDate`).",
    "meaning": "Internal start date of promocode validity."
  },
  "internalEndDate": {
    "type": "string",
    "format": "ISO datetime",
    "behavior": "Server-managed. Internal/normalised end date used by back-office logic.",
    "meaning": "Internal end date of promocode validity."
  },
  "note": {
    "type": "string",
    "default": "",
    "constraint": "max 200 characters",
    "behavior": "Internal-only note shown in the admin editor; not visible to customers.",
    "meaning": "Internal note for staff or operators."
  },
  "paused": {
    "type": "boolean",
    "behavior": "Toggled by the 'pause code' / 'release code' actions in the editor; saves through a dedicated endpoint.",
    "meaning": "Whether promocode usage is temporarily paused."
  },
  "completed": {
    "type": "boolean",
    "behavior": "Set true by the 'complete' action in the editor. Completed codes can be archived but not paused or deleted.",
    "meaning": "Whether the promocode campaign or rule is finished."
  },
  "archived": {
    "type": "boolean",
    "behavior": "Set true by the 'archive code' action; archived codes are read-only in the editor and can be restored via a 'restore' action.",
    "meaning": "Whether the promocode has been archived."
  },
  "deleted": {
    "type": "boolean",
    "behavior": "Soft-delete flag. The 'delete code' action calls a dedicated delete endpoint and navigates back to the list.",
    "meaning": "Whether the promocode is marked as deleted."
  },
  "visibility": {
    "type": "string",
    "behavior": "Defines whether the code is publicly listed or hidden. Likely values include `public` and private/internal variants.",
    "meaning": "Promocode visibility mode."
  },
  "placeDbNames": {
    "type": "array",
    "behavior": "Chain-only. List of branch tenant identifiers the promocode applies to. Edited via the dedicated `Venues` block on the chain main account; falls back to `dbName` when this field is empty on load.",
    "itemShape": "string"
  },
  "dbName": {
    "type": "string | array",
    "behavior": "Server-managed. As an array on a chain-level promocode, lists branches it belongs to (legacy alias for `placeDbNames`). As a string in single-tenant mode, identifies the tenant.",
    "meaning": "Technical tenant or database identifier(s)."
  },
  "groups": {
    "type": "array",
    "behavior": "Bulk-issued promocode groups (e.g. when a single rule generates many one-time codes).",
    "itemShape": {
      "groupId": {
        "type": "string",
        "meaning": "Group identifier."
      },
      "count": {
        "type": "number",
        "meaning": "How many codes were generated in this group."
      },
      "deleted": {
        "type": "boolean",
        "meaning": "Whether this group has been removed."
      },
      "createdAt": {
        "type": "string",
        "format": "ISO datetime",
        "meaning": "Group creation timestamp."
      },
      "updatedAt": {
        "type": "string",
        "format": "ISO datetime",
        "meaning": "Group update timestamp."
      }
    }
  },
  "history": {
    "type": "array",
    "behavior": "Server-managed audit log. Rendered in the editor's Logs panel for existing codes.",
    "itemShape": {
      "action": {
        "type": "string",
        "meaning": "History action type, for example `created`, `paused`, `released`, `completed`, `archived`, `restored`, `deleted`, or generic update events."
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
  "createdAt": {
    "type": "string",
    "format": "ISO datetime",
    "behavior": "Server-managed.",
    "meaning": "Promocode creation timestamp."
  },
  "updatedAt": {
    "type": "string",
    "format": "ISO datetime",
    "behavior": "Server-managed.",
    "meaning": "Promocode update timestamp."
  }
}
```
