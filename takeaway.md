# takeaway

JSON-shaped reference for the `takeaway` object.

Notes:

- This file mirrors the API response structure for a single area of channel type `takeaway`.
- It uses `jsonc` style so descriptions can live next to fields.
- The shape is the same `Area` shape used by `tablePayments` and `delivery`; `config.*` fields are surfaced according to channel-specific UX (e.g. takeaway exposes `useReadyForPickupStep`, `orderPhoneVerification`).
- Editor lives at the takeaway feature page and is gated by both the `canOrderTakeaway` company feature flag and the corresponding client-feature. The whole settings form is also disabled / partially hidden when the company is in chain-ordering sync mode.
- Working hours are edited in a dedicated WorkingHoursForm block; on save, `workTime` is stripped from the payload because the working-hours form has its own save call.
- Top-level fields stripped on save: `updatedAt`, `createdAt`, `position`.
- Validations enforced before save:
  - At least one entry in `paymentMethods` must be true (cash / card / bill / online).
  - `time.allowASAP || time.allowTime` must be true (otherwise the form refuses to save).
  - `orderAutoAcceptance.defaultPreparingTime >= 5` (when auto-acceptance is active).
- Phone verification toggle has a guard: enabling it shows a warning toast if the `smsDeliveryTakeaway` feature is not active, with a link to the SMS settings.
- Relations between `sections`, `categories`, and `items` are preserved through ID references.

```jsonc
{
  "_id": {
    "type": "string",
    "behavior": "Server-managed.",
    "meaning": "Internal area identifier."
  },
  "active": {
    "type": "boolean",
    "behavior": "When the company customization restricts feature-state changes for `canOrderTakeaway`, the toggle is hidden from non chain-owner users.",
    "meaning": "Global active state of the takeaway menu/channel."
  },
  "type": {
    "type": "string",
    "values": ["takeaway"],
    "meaning": "Area type — `takeaway`."
  },
  "default": {
    "type": "boolean",
    "meaning": "Whether this is the default takeaway channel."
  },
  "guid": {
    "type": "string",
    "behavior": "Server-managed.",
    "meaning": "Public or shareable short identifier."
  },
  "link": {
    "type": "string",
    "behavior": "Server-managed.",
    "meaning": "Public takeaway link."
  },
  "position": {
    "type": "number",
    "behavior": "Stripped from save payload.",
    "meaning": "Sort order among other channels."
  },
  "i18n": {
    "type": "object",
    "shape": {
      "<lang>": {
        "type": "object",
        "shape": {
          "name": { "type": "string", "meaning": "Localized display name." },
          "description": { "type": "string", "meaning": "Localized description, if any." }
        }
      }
    }
  },
  "emails": {
    "type": "array | object | null",
    "meaning": "Email-related configuration for notifications or routing, if used."
  },
  "posID": {
    "type": "string | null",
    "behavior": "Edited via a POS id search field (`type: 'locationPoint'`).",
    "meaning": "External POS identifier for the takeaway channel."
  },
  "createdAt": {
    "type": "string",
    "format": "ISO datetime",
    "behavior": "Stripped from save payload.",
    "meaning": "Object creation timestamp."
  },
  "updatedAt": {
    "type": "string",
    "format": "ISO datetime",
    "behavior": "Stripped from save payload.",
    "meaning": "Object update timestamp."
  },
  "categories": {
    "type": "array",
    "behavior": "Read-only in this editor — managed via the menu editor.",
    "itemShape": {
      "_id": { "type": "string" },
      "active": { "type": "boolean" },
      "createdAt": { "type": "string", "format": "ISO datetime" },
      "hurl": { "type": "string" },
      "i18n": {
        "type": "object",
        "shape": {
          "<lang>": {
            "type": "object",
            "shape": {
              "name": { "type": "string" },
              "description": { "type": "string" }
            }
          }
        }
      },
      "itemCount": { "type": "number" },
      "position": { "type": "number" },
      "section": { "type": "string", "meaning": "Reference to `sections[]._id`." },
      "posID": { "type": "string" },
      "posSync": {
        "type": "object",
        "shape": {
          "count": { "type": "number" },
          "syncCount": { "type": "number" },
          "version": { "type": "number" }
        }
      }
    }
  },
  "sections": {
    "type": "array",
    "behavior": "Read-only in this editor — managed via the menu editor.",
    "itemShape": {
      "_id": { "type": "string" },
      "active": { "type": "boolean" },
      "i18n": {
        "type": "object",
        "shape": {
          "<lang>": {
            "type": "object",
            "shape": {
              "name": { "type": "string" },
              "description": { "type": "string" }
            }
          }
        }
      },
      "mode": {
        "type": "object",
        "shape": {
          "type": {
            "type": "string",
            "values": ["interactive", "static", "link"]
          }
        }
      },
      "position": { "type": "number" }
    }
  },
  "items": {
    "type": "array",
    "behavior": "Read-only in this editor — managed via the menu editor.",
    "itemShape": {
      "_id": { "type": "string" },
      "active": { "type": "boolean" },
      "attributes": {
        "type": "array",
        "itemShape": "string  // e.g. 'SOLD_OUT'"
      },
      "category": { "type": "string", "meaning": "Reference to `categories[]._id`." },
      "createdAt": { "type": "string", "format": "ISO datetime" },
      "hurl": { "type": "string" },
      "i18n": {
        "type": "object",
        "shape": {
          "<lang>": {
            "type": "object",
            "shape": {
              "name": { "type": "string" },
              "description": { "type": "string" }
            }
          }
        }
      },
      "position": { "type": "number" },
      "price": { "type": "number" },
      "posID": { "type": "string" }
    }
  },
  "paymentMethods": {
    "type": "object",
    "behavior": "At least one method must be enabled. The `online` method is only rendered when company-level `onlinePayment` is true. UI marks the missing-payment-method state as an error on save.",
    "shape": {
      "cash": { "type": "boolean", "meaning": "Whether cash payment is allowed." },
      "card": { "type": "boolean", "meaning": "Whether POS terminal (card-on-delivery) payment is allowed." },
      "bill": { "type": "boolean", "meaning": "Whether bill / invoice-style payment is allowed." },
      "online": {
        "type": "boolean",
        "behavior": "Only available when `company.onlinePayment` is true.",
        "meaning": "Whether online payment is allowed."
      }
    }
  },
  "config": {
    "type": "object",
    "behavior": "Channel-level behavior. Saved via `updateAreaTakeaway`. Editor surfaces minimum-order, menu discount + categories + window, payment methods, time settings, order progress flag (company customization), pickup-ready flow, tips, auto-acceptance, description, and phone verification.",
    "shape": {
      "minOrderAmount": {
        "type": "number",
        "default": 0,
        "behavior": "Hidden when chain-ordering feature is active.",
        "meaning": "Minimum allowed order amount."
      },
      "menuDiscount": {
        "type": "array",
        "default": [],
        "behavior": "Editor only persists a single entry: `[{ orderAmount: 0, discount: <value> }]`. Discount value paired with `discountCategories`.",
        "itemShape": {
          "orderAmount": { "type": "number", "meaning": "Order amount threshold." },
          "discount": { "type": "number", "unit": "%", "meaning": "Discount value." }
        }
      },
      "discountCategories": {
        "type": "array",
        "itemShape": "string  // category._id",
        "behavior": "Edited via the DiscountCategoriesButton.",
        "meaning": "List of category IDs eligible for the menu discount."
      },
      "discountWorkTime": {
        "type": "array | null",
        "behavior": "Toggle in the menu-discount section: `permanent` ⇒ null, `limited_time` ⇒ schedule. Defaults to `company.workTime` (or a Mon–Sun 12:00–15:00 schedule) when limited-time mode is first enabled.",
        "itemShape": {
          "dayOfWeek": { "type": "number", "values": [0, 1, 2, 3, 4, 5, 6] },
          "active": { "type": "boolean" },
          "from": { "type": "string", "format": "HH:mm" },
          "till": { "type": "string", "format": "HH:mm" }
        }
      },
      "time": {
        "type": "object",
        "default": {
          "allowTime": true,
          "allowASAP": true,
          "delay": 15,
          "interval": 15,
          "minPreOrdersDayCount": 0,
          "maxPreOrdersDayCount": 0
        },
        "behavior": "Validation: `allowASAP || allowTime` must be true. `delay` falls back to 15 on save.",
        "shape": {
          "allowASAP": { "type": "boolean", "default": true, "meaning": "Allows ASAP orders." },
          "allowTime": { "type": "boolean", "default": true, "meaning": "Allows scheduled time." },
          "delay": { "type": "number", "default": 15, "unit": "minutes", "meaning": "Lead time before earliest available slot." },
          "interval": { "type": "number", "default": 15, "unit": "minutes", "meaning": "Time slot step." },
          "allowNonWorkingOrder": { "type": "boolean", "meaning": "Allows orders outside working hours." },
          "minPreOrdersDayCount": { "type": "number", "default": 0, "meaning": "Min days ahead for preorder." },
          "maxPreOrdersDayCount": { "type": "number", "default": 0, "meaning": "Max days ahead for preorder." },
          "preOrderSkipDelay": { "type": "boolean", "meaning": "Whether preorder flow can skip standard delay rules." }
        }
      },
      "menuSync": {
        "type": "boolean",
        "meaning": "Whether takeaway menu is synchronised with another source (POS / shared menu)."
      },
      "workTime": {
        "type": "array",
        "behavior": "Edited in the dedicated WorkingHoursForm block. Stripped from this form's save payload (the WorkingHoursForm calls its own save).",
        "itemShape": {
          "dayOfWeek": { "type": "number", "values": [0, 1, 2, 3, 4, 5, 6] },
          "active": { "type": "boolean" },
          "from": { "type": "string", "format": "HH:mm" },
          "till": { "type": "string", "format": "HH:mm" }
        }
      },
      "additionalFees": {
        "type": "object | null",
        "behavior": "Edited via the AdditionalPayment block (hidden when chain-ordering features are active).",
        "shape": {
          "items": {
            "type": "array",
            "itemShape": {
              "id": { "type": "string" },
              "type": { "type": "string", "values": ["areaFee", "..."] },
              "amount": { "type": "number" },
              "posID": { "type": "string" },
              "i18n": {
                "type": "object",
                "shape": {
                  "<lang>": {
                    "type": "object",
                    "shape": {
                      "name": { "type": "string" }
                    }
                  }
                }
              }
            }
          }
        }
      },
      "description": {
        "type": "string",
        "default": "",
        "constraint": "max 2000 characters",
        "behavior": "Multi-line text field with character counter; supports emoji input.",
        "meaning": "Takeaway-specific description shown to customers."
      },
      "orderAutoAcceptance": {
        "type": "object",
        "behavior": "When `active` is true, sub-fields are revealed in the editor. Validation: `defaultPreparingTime >= 5`. Defaults seeded: `defaultPreparingTime = 15`, `delayMinutes = 2`. Disabling `active` resets `defaultPreparingTime` to 15. Hidden when chain-ordering feature is active.",
        "shape": {
          "active": { "type": "boolean" },
          "defaultPreparingTime": {
            "type": "number",
            "unit": "minutes",
            "default": 15,
            "constraint": ">= 5",
            "meaning": "Default preparation time."
          },
          "delayMinutes": {
            "type": "number | null",
            "default": 2,
            "constraint": "<= 15",
            "behavior": "null = immediate acceptance, otherwise delay in minutes (max 15).",
            "meaning": "Acceptance delay."
          },
          "acceptNonWorkingHoursPreOrder": {
            "type": "boolean",
            "default": true,
            "meaning": "Whether pre-orders placed outside working hours are auto-accepted."
          }
        }
      },
      "useReadyForPickupStep": {
        "type": "boolean",
        "behavior": "When true, an extra 'ready for pickup' status is used in the order lifecycle and the `orderAutoClose` block is revealed.",
        "meaning": "Whether the takeaway flow uses an explicit 'ready for pickup' step."
      },
      "orderAutoClose": {
        "type": "object",
        "behavior": "Only visible while `useReadyForPickupStep` is true. When `active` is true, takeaway orders are auto-closed `delayMinutes` after going to 'ready for pickup'.",
        "shape": {
          "active": { "type": "boolean" },
          "delayMinutes": { "type": "number", "unit": "minutes", "meaning": "Delay before auto-closing the order." }
        }
      },
      "defaultTips": {
        "type": "number",
        "behavior": "Edited via the TipsBlock (hidden when chain-ordering feature is active).",
        "meaning": "Default tip value offered in the takeaway flow."
      },
      "tipsSettings": {
        "type": "object",
        "shape": {
          "defaultTipsType": { "type": "string", "values": ["REQUIRED", "..."] },
          "recipient": { "type": "string", "values": ["restaurant", "staff"] },
          "recipientAccount": {
            "type": "string | null",
            "values": ["globalTips", "adyen", "noSplitting", null]
          },
          "customTipsList": {
            "type": "array",
            "itemShape": {
              "value": { "type": "number" }
            }
          }
        }
      },
      "orderPhoneVerification": {
        "type": "boolean",
        "behavior": "Enabling it shows a warning toast if `smsDeliveryTakeaway` feature is not active (with a link to the SMS settings page). When enabled, an attention banner is rendered below the toggle.",
        "meaning": "Whether phone verification is required during takeaway ordering."
      }
    }
  },
  "posSync": {
    "type": "object",
    "behavior": "Server-managed.",
    "shape": {
      "count": { "type": "number" },
      "syncCount": { "type": "number" },
      "version": { "type": "number" }
    }
  },
  "location_points": {
    "type": "array",
    "meaning": "Location points related to takeaway fulfillment / pickup. Usually empty."
  }
}
```
