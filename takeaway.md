# takeaway

JSON-shaped reference for the `takeaway` object.

Notes:
- This file intentionally mirrors the API response structure.
- It uses `jsonc` style so descriptions can live next to fields.
- Relations between `sections`, `categories`, and `items` are preserved through ID references.

```jsonc
{
  "_id": {
    "type": "string",
    "meaning": "Internal takeaway configuration identifier."
  },
  "active": {
    "type": "boolean",
    "meaning": "Global active state of the takeaway menu/channel."
  },
  "categories": {
    "type": "array",
    "meaning": "List of takeaway categories. Categories usually belong to sections and group items.",
    "itemShape": {
      "_id": {
        "type": "string",
        "meaning": "Internal category identifier."
      },
      "active": {
        "type": "boolean",
        "meaning": "Category active state."
      },
      "createdAt": {
        "type": "string",
        "format": "ISO datetime",
        "meaning": "Category creation timestamp."
      },
      "hurl": {
        "type": "string",
        "meaning": "Human-readable URL slug for the category."
      },
      "i18n": {
        "type": "object",
        "meaning": "Localized category content by language.",
        "shape": {
          "<lang>": {
            "type": "object",
            "meaning": "Localized category entry.",
            "shape": {
              "name": {
                "type": "string",
                "meaning": "Localized category name."
              },
              "description": {
                "type": "string",
                "meaning": "Localized category description. Can be empty."
              }
            }
          }
        }
      },
      "itemCount": {
        "type": "number",
        "meaning": "Number of items assigned to the category. May be stored or cached."
      },
      "position": {
        "type": "number",
        "meaning": "Category sort order inside its section."
      },
      "section": {
        "type": "string",
        "meaning": "Reference to `sections[]._id` that owns this category."
      },
      "posID": {
        "type": "string",
        "meaning": "External POS identifier for the category, if synced with POS."
      },
      "posSync": {
        "type": "object",
        "meaning": "POS synchronization metadata for the category.",
        "shape": {
          "count": {
            "type": "number",
            "meaning": "Sync-related counter, likely total operations or linked records."
          },
          "syncCount": {
            "type": "number",
            "meaning": "Counter of completed sync operations or synced records."
          },
          "version": {
            "type": "number",
            "meaning": "POS sync schema/version number."
          }
        }
      }
    }
  },
  "config": {
    "type": "object",
    "meaning": "Takeaway channel configuration: pricing thresholds, scheduling, discounts, tips, sync, and order handling.",
    "shape": {
      "minOrderAmount": {
        "type": "number",
        "meaning": "Minimum allowed order amount."
      },
      "menuDiscount": {
        "type": "array",
        "meaning": "Discount rules based on order amount.",
        "itemShape": {
          "orderAmount": {
            "type": "number",
            "meaning": "Order amount threshold required to trigger the discount."
          },
          "discount": {
            "type": "number",
            "meaning": "Discount value, most likely percentage unless product logic states otherwise."
          }
        }
      },
      "time": {
        "type": "object",
        "meaning": "Takeaway ordering time settings.",
        "shape": {
          "delay": {
            "type": "number",
            "meaning": "Default lead time before the earliest available order time."
          },
          "interval": {
            "type": "number",
            "meaning": "Time slot interval step."
          },
          "allowASAP": {
            "type": "boolean",
            "meaning": "Allows ASAP orders."
          },
          "allowTime": {
            "type": "boolean",
            "meaning": "Allows choosing a scheduled time."
          },
          "allowNonWorkingOrder": {
            "type": "boolean",
            "meaning": "Allows placing orders outside configured working hours."
          },
          "maxPreOrdersDayCount": {
            "type": "number",
            "meaning": "Maximum number of days ahead available for preorder."
          },
          "minPreOrdersDayCount": {
            "type": "number",
            "meaning": "Minimum number of days ahead required for preorder."
          }
        }
      },
      "discountCategories": {
        "type": "array",
        "meaning": "List of category IDs eligible for menu discount logic.",
        "itemShape": "string"
      },
      "defaultTips": {
        "type": "number",
        "meaning": "Default tip value offered in takeaway flow."
      },
      "menuSync": {
        "type": "boolean",
        "meaning": "Whether takeaway menu is synchronized with another source, likely POS or shared menu."
      },
      "workTime": {
        "type": "array",
        "meaning": "Takeaway-specific working hours. Usually overrides or specializes general company working hours for this channel.",
        "itemShape": {
          "dayOfWeek": {
            "type": "number",
            "meaning": "Day index, usually `0..6`. Exact mapping should be confirmed in product logic."
          },
          "active": {
            "type": "boolean",
            "meaning": "Whether takeaway accepts orders on that day."
          },
          "from": {
            "type": "string",
            "meaning": "Opening time for takeaway orders."
          },
          "till": {
            "type": "string",
            "meaning": "Closing time for takeaway orders."
          }
        }
      },
      "additionalFees": {
        "type": "object | array | null",
        "meaning": "Additional fee configuration for takeaway orders, if used."
      },
      "discountWorkTime": {
        "type": "object | array | null",
        "meaning": "Time-based discount schedule configuration, if used."
      },
      "description": {
        "type": "string",
        "meaning": "Takeaway-specific description. Can be empty."
      },
      "orderAutoAcceptance": {
        "type": "object",
        "meaning": "Automatic order acceptance behavior.",
        "shape": {
          "defaultPreparingTime": {
            "type": "number",
            "meaning": "Default preparation time for accepted orders."
          },
          "delayMinutes": {
            "type": "number",
            "meaning": "Extra acceptance delay added by automation logic."
          }
        }
      },
      "orderPhoneVerification": {
        "type": "boolean",
        "meaning": "Whether phone verification is required during takeaway ordering."
      }
    }
  },
  "createdAt": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "Takeaway object creation timestamp."
  },
  "default": {
    "type": "boolean",
    "meaning": "Whether this takeaway configuration is the default channel/menu."
  },
  "emails": {
    "type": "array | object | null",
    "meaning": "Email-related configuration for takeaway notifications or routing, if used."
  },
  "guid": {
    "type": "string",
    "meaning": "Public or shareable short identifier used in links or routing."
  },
  "i18n": {
    "type": "object",
    "meaning": "Localized takeaway-level content by language.",
    "shape": {
      "<lang>": {
        "type": "object",
        "meaning": "Localized takeaway object entry.",
        "shape": {
          "name": {
            "type": "string",
            "meaning": "Localized takeaway display name."
          },
          "description": {
            "type": "string",
            "meaning": "Localized takeaway description, if supported. Can be absent or empty."
          }
        }
      }
    }
  },
  "items": {
    "type": "array",
    "meaning": "List of menu items available in takeaway.",
    "itemShape": {
      "_id": {
        "type": "string",
        "meaning": "Internal item identifier."
      },
      "active": {
        "type": "boolean",
        "meaning": "Item active state."
      },
      "attributes": {
        "type": "array",
        "meaning": "List of item attribute flags or tags such as `SOLD_OUT`. Can be empty.",
        "itemShape": "string"
      },
      "category": {
        "type": "string",
        "meaning": "Reference to `categories[]._id` that owns this item."
      },
      "createdAt": {
        "type": "string",
        "format": "ISO datetime",
        "meaning": "Item creation timestamp."
      },
      "hurl": {
        "type": "string",
        "meaning": "Human-readable URL slug for the item."
      },
      "i18n": {
        "type": "object",
        "meaning": "Localized item content by language.",
        "shape": {
          "<lang>": {
            "type": "object",
            "meaning": "Localized item entry.",
            "shape": {
              "name": {
                "type": "string",
                "meaning": "Localized item name."
              },
              "description": {
                "type": "string",
                "meaning": "Localized item description. Can be empty or absent."
              }
            }
          }
        }
      },
      "position": {
        "type": "number",
        "meaning": "Item sort order inside its category."
      },
      "price": {
        "type": "number",
        "meaning": "Item price in the system's stored minor units or internal price format."
      },
      "posID": {
        "type": "string",
        "meaning": "External POS identifier for the item, if synced."
      }
    }
  },
  "link": {
    "type": "string",
    "meaning": "Public takeaway link."
  },
  "paymentMethods": {
    "type": "object",
    "meaning": "Enabled payment methods for takeaway.",
    "shape": {
      "<paymentMethod>": {
        "type": "boolean",
        "meaning": "Whether the given payment method is enabled."
      },
      "cash": {
        "type": "boolean",
        "meaning": "Whether cash payment is allowed."
      }
    }
  },
  "posID": {
    "type": "string",
    "meaning": "External POS identifier for the takeaway channel itself."
  },
  "position": {
    "type": "number",
    "meaning": "Takeaway sort order among other channels or menus."
  },
  "sections": {
    "type": "array",
    "meaning": "Top-level menu sections used to group categories.",
    "itemShape": {
      "_id": {
        "type": "string",
        "meaning": "Internal section identifier."
      },
      "active": {
        "type": "boolean",
        "meaning": "Section active state."
      },
      "i18n": {
        "type": "object",
        "meaning": "Localized section content by language.",
        "shape": {
          "<lang>": {
            "type": "object",
            "meaning": "Localized section entry.",
            "shape": {
              "name": {
                "type": "string",
                "meaning": "Localized section name."
              },
              "description": {
                "type": "string",
                "meaning": "Localized section description. Can be empty."
              }
            }
          }
        }
      },
      "mode": {
        "type": "object",
        "meaning": "Section behavior mode.",
        "shape": {
          "type": {
            "type": "string",
            "meaning": "Section mode type, for example `interactive`, `static`, or `link`."
          }
        }
      },
      "position": {
        "type": "number",
        "meaning": "Section sort order."
      }
    }
  },
  "type": {
    "type": "string",
    "meaning": "Channel type. For this object it is expected to be `takeaway`."
  },
  "updatedAt": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "Takeaway object update timestamp."
  },
  "posSync": {
    "type": "object",
    "meaning": "POS synchronization metadata for the takeaway object.",
    "shape": {
      "count": {
        "type": "number",
        "meaning": "Sync-related counter, likely total operations or linked records."
      },
      "syncCount": {
        "type": "number",
        "meaning": "Counter of completed sync operations or synced records."
      },
      "version": {
        "type": "number",
        "meaning": "POS sync schema/version number."
      }
    }
  },
  "location_points": {
    "type": "array",
    "meaning": "Location points related to takeaway fulfillment, pickup, or service coverage. Can be empty.",
    "itemShape": "object"
  }
}
```

