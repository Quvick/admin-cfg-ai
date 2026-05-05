# Delivery

JSON-shaped reference for the `delivery` object.

Notes:

- This file mirrors the API response structure for a single area of channel type `delivery`.
- It uses `jsonc` style so descriptions can live next to fields.
- The shape is the same `Area` shape used by `takeaway` and `tablePayments`; the difference is that delivery surfaces zone-strategy configuration (`distances`, `polygons`, `fixed`), door-delivery toggles, address-field rules, and a delivery-service / courier integration.
- Editor lives at the delivery feature page and is gated by both the `canOrderDelivery` company feature flag and the corresponding client-feature. The whole settings form is also disabled / partially hidden when the company is in chain-ordering sync mode.
- Working hours are edited in a dedicated WorkingHoursForm block; on save, `workTime` is stripped from the payload because the working-hours form has its own save call. `allowNoStreetNumber` and `position` are also stripped on save.
- Validations enforced before save:
  - At least one entry in `paymentMethods` must be true (cash / card / bill / online).
  - `time.allowASAP || time.allowTime` must be true.
  - `orderAutoAcceptance.defaultPreparingTime >= 5` (when auto-acceptance is active).
- Phone verification toggle has a guard: enabling it shows a warning toast if the `smsDeliveryTakeaway` feature is not active.
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
    "behavior": "When the company customization restricts feature-state changes for `canOrderDelivery`, the toggle is hidden from non chain-owner users.",
    "meaning": "Global active state of the delivery channel."
  },
  "type": {
    "type": "string",
    "values": ["delivery"],
    "meaning": "Area type — `delivery`."
  },
  "default": {
    "type": "boolean",
    "meaning": "Whether this is the default channel/menu."
  },
  "guid": {
    "type": "string",
    "behavior": "Server-managed.",
    "meaning": "Public or shareable short identifier."
  },
  "link": {
    "type": "string",
    "behavior": "Server-managed.",
    "meaning": "Public delivery link."
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
    "behavior": "Edited via a POS id search field.",
    "meaning": "External POS identifier for the delivery channel."
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
    "behavior": "At least one method must be enabled. The `online` method is only available when company-level `onlinePayment` is true.",
    "shape": {
      "card": { "type": "boolean", "meaning": "Whether card payment is allowed." },
      "cash": { "type": "boolean", "meaning": "Whether cash payment is allowed." },
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
    "behavior": "Channel-level behavior. Saved via `updateAreaDelivery`. Editor surfaces the delivery-zone strategy (`type` + the matching list editor), pricing rules, time settings, address-field rules, door-delivery toggle, description, tips, auto-acceptance, and phone verification.",
    "shape": {
      "type": {
        "type": "string",
        "values": ["fixed", "radius", "polygons"],
        "default": "fixed",
        "behavior": "Delivery-zone strategy. Selecting one renders only the matching list (FixedPriceForm / RadiusList / PolygonsList).",
        "meaning": "Delivery zone strategy type."
      },
      "fixed": {
        "type": "array",
        "behavior": "Active when `type === 'fixed'`. Editor: FixedPriceForm.",
        "itemShape": {
          "orderAmount": { "type": "number", "meaning": "Order amount threshold." },
          "deliveryAmount": { "type": "number", "meaning": "Delivery fee in stored minor units." },
          "costProperties": {
            "type": "object",
            "shape": {
              "type": {
                "type": "string",
                "values": ["DEFINED", "UNDEFINED"],
                "meaning": "Pricing mode."
              },
              "i18n": {
                "type": "object",
                "shape": {
                  "<lang>": {
                    "type": "object",
                    "shape": {
                      "description": { "type": "string" },
                      "name": { "type": "string" }
                    }
                  }
                }
              }
            }
          },
          "posID": { "type": "string" }
        }
      },
      "distances": {
        "type": "array",
        "behavior": "Active when `type === 'radius'`. Editor: RadiusList. Each band has its own deliveryCost rule list and a `radius` (capped at MAX_DELIVERY_RADIUS = 30 km).",
        "itemShape": {
          "id": { "type": "string", "meaning": "Internal band id." },
          "radius": { "type": "string | number", "constraint": "<= 30", "meaning": "Maximum distance radius." },
          "enabled": { "type": "boolean", "meaning": "Whether this distance rule is active." },
          "deliveryCost": {
            "type": "array",
            "itemShape": {
              "orderAmount": { "type": "number" },
              "deliveryAmount": { "type": "number" },
              "costProperties": {
                "type": "object",
                "shape": {
                  "type": {
                    "type": "string",
                    "values": ["DEFINED", "UNDEFINED"]
                  },
                  "i18n": {
                    "type": "object",
                    "shape": {
                      "<lang>": {
                        "type": "object",
                        "shape": {
                          "description": { "type": "string" },
                          "name": { "type": "string" }
                        }
                      }
                    }
                  }
                }
              },
              "posID": { "type": "string" }
            }
          }
        }
      },
      "polygons": {
        "type": "array",
        "behavior": "Active when `type === 'polygons'`. Editor: PolygonsList. Each polygon defines its own boundary points and pricing.",
        "itemShape": {
          "id": { "type": "string" },
          "name": { "type": "string", "meaning": "Polygon zone name." },
          "enabled": { "type": "boolean" },
          "points": {
            "type": "array",
            "itemShape": {
              "lat": { "type": "number" },
              "lng": { "type": "number" }
            }
          },
          "deliveryCost": {
            "type": "array",
            "itemShape": {
              "orderAmount": { "type": "number" },
              "deliveryAmount": { "type": "number" },
              "costProperties": {
                "type": "object",
                "shape": {
                  "type": {
                    "type": "string",
                    "values": ["DEFINED", "UNDEFINED"]
                  }
                }
              },
              "posID": { "type": "string" }
            }
          }
        }
      },
      "deliveryCost": {
        "type": "array",
        "meaning": "Fallback / legacy delivery cost rules used in some configurations."
      },
      "strictMatch": {
        "type": "boolean",
        "default": true,
        "meaning": "Whether address matching must strictly fit a configured delivery rule."
      },
      "toDoor": {
        "type": "object",
        "behavior": "Door-delivery configuration shown only when relevant zone settings are configured.",
        "shape": {
          "active": { "type": "boolean", "meaning": "Whether door delivery is enabled." },
          "price": { "type": "number", "meaning": "Optional door-delivery surcharge." },
          "forcePublic": { "type": "boolean", "meaning": "Whether door-delivery option is always publicly visible." }
        }
      },
      "hideFloorApartmentField": {
        "type": "boolean",
        "meaning": "Whether floor/apartment address fields are hidden during checkout."
      },
      "allowNoStreetNumber": {
        "type": "boolean",
        "behavior": "Stripped from save payload.",
        "meaning": "Whether orders are allowed without a street number."
      },
      "menuDiscount": {
        "type": "array",
        "behavior": "Discount rules paired with `discountCategories` and `discountWorkTime`. The editor (DiscountList) supports multiple thresholds.",
        "itemShape": {
          "discount": { "type": "number", "unit": "%" },
          "orderAmount": { "type": "number" }
        }
      },
      "discountCategories": {
        "type": "array",
        "itemShape": "string  // category._id",
        "meaning": "List of category IDs eligible for discount logic."
      },
      "discountWorkTime": {
        "type": "array | null",
        "behavior": "Toggle: `permanent` ⇒ null, `limited_time` ⇒ schedule. Defaults to `company.workTime` (or a Mon–Sun 12:00–15:00 schedule) when limited-time mode is first enabled.",
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
        "behavior": "Validation: `allowASAP || allowTime` must be true.",
        "shape": {
          "allowASAP": { "type": "boolean", "default": true },
          "allowTime": { "type": "boolean", "default": true },
          "delay": { "type": "number", "default": 15, "unit": "minutes" },
          "interval": { "type": "number", "default": 15, "unit": "minutes" },
          "preOrderSkipDelay": { "type": "boolean" },
          "allowNonWorkingOrder": { "type": "boolean" },
          "minPreOrdersDayCount": { "type": "number", "default": 0 },
          "maxPreOrdersDayCount": { "type": "number", "default": 0 }
        }
      },
      "menuSync": {
        "type": "boolean",
        "meaning": "Whether delivery menu is synchronised with another source (POS / shared menu)."
      },
      "workTime": {
        "type": "array",
        "behavior": "Edited in the dedicated WorkingHoursForm block. Stripped from this form's save payload.",
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
        "behavior": "Multi-line text field with character counter; supports emoji input.",
        "meaning": "Delivery-specific description shown to customers."
      },
      "orderAutoAcceptance": {
        "type": "object",
        "behavior": "When `active` is true, sub-fields are revealed. Validation: `defaultPreparingTime >= 5`. Defaults seeded: `defaultPreparingTime = 15`, `delayMinutes = 2`. Hidden when chain-ordering feature is active.",
        "shape": {
          "active": { "type": "boolean" },
          "defaultPreparingTime": {
            "type": "number",
            "unit": "minutes",
            "default": 15,
            "constraint": ">= 5"
          },
          "delayMinutes": {
            "type": "number | null",
            "default": 2,
            "constraint": "<= 15",
            "behavior": "null = immediate acceptance, otherwise delay in minutes (max 15)."
          },
          "acceptNonWorkingHoursPreOrder": {
            "type": "boolean",
            "default": true
          }
        }
      },
      "defaultTips": {
        "type": "number | null",
        "behavior": "Edited via the TipsBlock (hidden when chain-ordering feature is active).",
        "meaning": "Default tip value offered in the delivery flow."
      },
      "tipsSettings": {
        "type": "object",
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
              "value": { "type": "number" }
            }
          }
        }
      },
      "orderPhoneVerification": {
        "type": "boolean",
        "behavior": "Enabling it shows a warning toast if `smsDeliveryTakeaway` feature is not active.",
        "meaning": "Whether phone verification is required during delivery ordering."
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
    "meaning": "Location points related to delivery fulfillment / channel binding. Usually empty."
  }
}
```
