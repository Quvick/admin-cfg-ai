# tablePayments

JSON-shaped reference for the `tablePayments` object.

Notes:

- This file mirrors the API response structure for a single area of channel type `simple` (used as the QR / table-payments area).
- It uses `jsonc` style so descriptions can live next to fields.
- The shape is the same `Area` shape used by `takeaway` and `delivery`; the differences are which `config.*` fields are surfaced in the editor and which payment methods are exposed.
- Editor lives at the QR / table-payments feature page and is gated by both the `posPaymentTable` company feature flag and a corresponding client-feature.
- The settings form is split into the area's `config` (saved via the dedicated table-payments save endpoint) and several adjacent blocks: tips, additional payments, QR/bill defaults, additional settings.
- `location_points`, `categories`, `sections`, `items`, and most top-level fields are read-only in this editor — they are managed in their respective tools (menu editor, location-points page, etc.).
- Two booleans on `config` are seeded from company-level customizations (`tablePaymentDisableSplit`, `QRPaymentsNoLimit`) when missing.
- Relations between `sections`, `categories`, `items`, and `location_points` are preserved through ID references.

```jsonc
{
  "_id": {
    "type": "string",
    "behavior": "Server-managed.",
    "meaning": "Internal area identifier."
  },
  "active": {
    "type": "boolean",
    "behavior": "Top-level area on/off. Toggled at the platform level.",
    "meaning": "Global active state of the tablePayments area/channel."
  },
  "type": {
    "type": "string",
    "values": ["simple"],
    "behavior": "Discriminates this area as the table-payments / QR-payments channel.",
    "meaning": "Area type — `simple` for tablePayments."
  },
  "default": {
    "type": "boolean",
    "meaning": "Whether this is the default area for this type."
  },
  "guid": {
    "type": "string",
    "behavior": "Server-managed.",
    "meaning": "Public or shareable short identifier used in links or routing."
  },
  "link": {
    "type": "string",
    "behavior": "Server-managed.",
    "meaning": "Public link for the area/channel."
  },
  "position": {
    "type": "number",
    "behavior": "Stripped from save payload.",
    "meaning": "Sort order of this area among other areas/menus."
  },
  "i18n": {
    "type": "object",
    "behavior": "Localized area-level content.",
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
  "itemCount": {
    "type": "number",
    "behavior": "Server-derived counter.",
    "meaning": "Total number of items in the area."
  },
  "createdAt": {
    "type": "string",
    "format": "ISO datetime",
    "behavior": "Server-managed; stripped from save payload.",
    "meaning": "Area creation timestamp."
  },
  "updatedAt": {
    "type": "string",
    "format": "ISO datetime",
    "behavior": "Server-managed; stripped from save payload.",
    "meaning": "Area update timestamp."
  },
  "hideFromTableReservation": {
    "type": "boolean",
    "meaning": "Whether this area should be hidden from table reservation flows."
  },
  "posID": {
    "type": "string | null",
    "meaning": "External POS identifier for the area."
  },
  "allowAreaOrder": {
    "type": "boolean",
    "behavior": "When true, ordering is allowed directly within this area (not just bill payment).",
    "meaning": "Whether ordering is allowed within this area."
  },
  "categories": {
    "type": "array",
    "behavior": "Read-only in this editor — managed via the menu editor.",
    "meaning": "Menu categories. Categories belong to sections and group items.",
    "itemShape": {
      "_id": { "type": "string", "meaning": "Internal category identifier." },
      "active": { "type": "boolean", "meaning": "Category active state." },
      "createdAt": { "type": "string", "format": "ISO datetime" },
      "hurl": { "type": "string", "meaning": "Human-readable URL slug for the category." },
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
    "meaning": "Top-level menu sections grouping categories.",
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
    "meaning": "Menu items in this area.",
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
      "price": { "type": "number", "meaning": "Item price in stored minor units / internal price format." },
      "posID": { "type": "string" }
    }
  },
  "paymentMethods": {
    "type": "object",
    "behavior": "QR / table payment area exposes these payment toggles. At least one method must be enabled when the area is in use; UI marks empty selection as an error on save.",
    "shape": {
      "card": { "type": "boolean", "meaning": "Whether card payment is allowed." },
      "cash": { "type": "boolean", "meaning": "Whether cash payment is allowed." },
      "hotel_room": { "type": "boolean", "meaning": "Whether payment to a hotel room is allowed." },
      "online": {
        "type": "boolean",
        "behavior": "Only relevant when company-level `onlinePayment` is true.",
        "meaning": "Whether online payment is allowed."
      },
      "bill": { "type": "boolean", "meaning": "Whether billing/invoice-style payment is allowed." }
    }
  },
  "config": {
    "type": "object",
    "behavior": "Area-level behavior. Saved via `updateAreaTablePayments`. Editor surfaces tip settings, split-payment, 24/7 mode, default page mode, and selector behavior.",
    "shape": {
      "discountCategories": {
        "type": "array",
        "itemShape": "string  // category._id",
        "meaning": "List of category IDs eligible for discount logic."
      },
      "menuDiscount": {
        "type": "array",
        "meaning": "Discount rules based on order amount.",
        "itemShape": {
          "discount": { "type": "number", "meaning": "Discount value (most likely percentage)." },
          "orderAmount": { "type": "number", "meaning": "Order amount threshold." }
        }
      },
      "defaultDirection": {
        "type": "object",
        "meaning": "Default landing destination for this area.",
        "shape": {
          "type": {
            "type": "string",
            "values": ["main", "section"],
            "meaning": "Destination type."
          },
          "value": {
            "type": "string | null",
            "meaning": "Target value for the selected destination type (e.g. a section id)."
          }
        }
      },
      "additionalFees": {
        "type": "object | null",
        "behavior": "Edited in the AdditionalPayment block adjacent to this form.",
        "shape": {
          "items": {
            "type": "array",
            "itemShape": {
              "id": { "type": "string", "meaning": "Internal fee entry identifier." },
              "type": { "type": "string", "values": ["areaFee", "..."] },
              "amount": { "type": "number", "meaning": "Fee amount in stored minor units." },
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
      "discountWorkTime": {
        "type": "array | null",
        "meaning": "Time-based discount schedule (per-day windows) when discounts are time-limited; null when discount is permanent."
      },
      "orderAutoAcceptance": {
        "type": "object",
        "behavior": "When `active` is true, orders are auto-accepted. Sub-fields are only editable while `active` is true. Validation: `defaultPreparingTime >= 5`. Default seeded values: `defaultPreparingTime = 15`, `delayMinutes = 2`.",
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
            "meaning": "Extra acceptance delay."
          },
          "acceptNonWorkingHoursPreOrder": {
            "type": "boolean",
            "default": true,
            "meaning": "Whether pre-orders placed outside working hours are auto-accepted."
          }
        }
      },
      "defaultTips": {
        "type": "number | null",
        "behavior": "Edited via the TipsBlock. null disables a default suggested tip.",
        "meaning": "Default tip value offered in this flow."
      },
      "defaultTablePaymentsTips": {
        "type": "number",
        "meaning": "Default tip specifically for table payments."
      },
      "tipsSettings": {
        "type": "object",
        "behavior": "Edited via the TipsBlock. Drives tip suggestion behavior and routing.",
        "shape": {
          "defaultTipsType": {
            "type": "string",
            "values": ["REQUIRED", "..."],
            "meaning": "Tip requirement/default mode."
          },
          "recipient": {
            "type": "string",
            "values": ["restaurant", "staff"],
            "meaning": "Who receives the tips."
          },
          "recipientAccount": {
            "type": "string | null",
            "values": ["globalTips", "adyen", "noSplitting", null],
            "meaning": "Account/balance destination for tips."
          },
          "customTipsList": {
            "type": "array",
            "itemShape": {
              "value": { "type": "number", "meaning": "Tip amount/percentage option." }
            },
            "meaning": "Custom tip suggestion list."
          }
        }
      },
      "tablePaymentDisableSplit": {
        "type": "boolean",
        "behavior": "When true, customers cannot pay separately at the same table. Seeded from the company customization `tablePaymentDisableSplit` if not set on the area. UI exposes the inverse (`allow_pay_separetlly`).",
        "meaning": "Disables split-bill payment."
      },
      "QRPaymentsNoLimit": {
        "type": "boolean",
        "behavior": "When true, QR / table payments are allowed 24/7 regardless of working hours. Seeded from the company customization `QRPaymentsNoLimit` if not set on the area.",
        "meaning": "24/7 service flag."
      },
      "QRPaymentUseBillPage": {
        "type": "boolean",
        "default": false,
        "behavior": "Default landing page for the QR flow: `false` → table page, `true` → bill page.",
        "meaning": "Whether QR payments default to the bill page."
      },
      "isQRPaymentUncheckedListState": {
        "type": "boolean",
        "behavior": "Behavior of the order-items selector on the bill: when true, items start unchecked; when false, items start selected.",
        "meaning": "Initial selector state for QR payments."
      },
      "minOrderAmount": {
        "type": "number",
        "meaning": "Minimum allowed order amount."
      },
      "menuSync": {
        "type": "boolean",
        "meaning": "Whether the menu is synchronised with another source (POS or shared chain menu)."
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
    "behavior": "Tables / seats / service points assigned to this area. Read-only in this editor — managed in the location-points page.",
    "itemShape": {
      "_id": { "type": "string" },
      "active": { "type": "boolean" },
      "area": { "type": "string", "meaning": "Reference to the parent `tablePayments._id`." },
      "createdAt": { "type": "string", "format": "ISO datetime" },
      "guid": { "type": "string", "meaning": "Public/short identifier for the location point." },
      "i18n": {
        "type": "object",
        "shape": {
          "<lang>": {
            "type": "object",
            "shape": {
              "name": { "type": "string", "meaning": "Localized table/point label." }
            }
          }
        }
      },
      "link": { "type": "string", "meaning": "Public link assigned to the location point." },
      "posID": { "type": "string" },
      "position": { "type": "number" },
      "seats": { "type": "number", "meaning": "Number of seats." },
      "updatedAt": { "type": "string", "format": "ISO datetime" },
      "hideFromTableReservation": {
        "type": "boolean",
        "meaning": "Whether this specific location point is hidden from table reservation flows."
      },
      "qrManagementCode": {
        "type": "string",
        "meaning": "QR management code used for table-side QR generation."
      },
      "qrManagementCodes": {
        "type": "array",
        "itemShape": "string"
      }
    }
  }
}
```
