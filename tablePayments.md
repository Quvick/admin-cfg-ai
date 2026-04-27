# tablePayments

JSON-shaped reference for the `tablePayments` object.

Notes:
- This file intentionally mirrors the API response structure.
- It uses `jsonc` style so descriptions can live next to fields.
- Relations between `sections`, `categories`, `items`, and `location_points` are preserved through ID references.
- The overall menu structure is similar to `takeaway`, but this object also includes table/location-specific payment behavior.

```jsonc
{
  "_id": {
    "type": "string",
    "meaning": "Internal tablePayments configuration identifier."
  },
  "active": {
    "type": "boolean",
    "meaning": "Global active state of the tablePayments area/channel."
  },
  "categories": {
    "type": "array",
    "meaning": "List of menu categories. Categories usually belong to sections and group items.",
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
                "meaning": "Localized category description. Can be empty or absent."
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
        "meaning": "External POS identifier for the category, if synced."
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
    "meaning": "Table payment area configuration, including discounts, direction, fees, auto-acceptance, and tips.",
    "shape": {
      "discountCategories": {
        "type": "array",
        "meaning": "List of category IDs eligible for discount logic.",
        "itemShape": "string"
      },
      "menuDiscount": {
        "type": "array",
        "meaning": "Discount rules based on order amount.",
        "itemShape": {
          "discount": {
            "type": "number",
            "meaning": "Discount value, most likely percentage unless product logic states otherwise."
          },
          "orderAmount": {
            "type": "number",
            "meaning": "Order amount threshold required to trigger the discount."
          }
        }
      },
      "defaultDirection": {
        "type": "object",
        "meaning": "Default landing destination for this area/channel.",
        "shape": {
          "type": {
            "type": "string",
            "meaning": "Destination type, likely enum-like values such as `section`, and possibly other navigation targets."
          },
          "value": {
            "type": "string",
            "meaning": "Target value for the selected destination type, for example a section slug or identifier."
          }
        }
      },
      "allowAreaOrder": {
        "type": "boolean",
        "meaning": "Whether ordering is allowed directly within this area."
      },
      "additionalFees": {
        "type": "object | null",
        "meaning": "Additional fee configuration applied in this area.",
        "shape": {
          "items": {
            "type": "array",
            "meaning": "Configured extra fee entries.",
            "itemShape": {
              "posID": {
                "type": "string",
                "meaning": "External POS identifier for the fee item, if synced."
              },
              "amount": {
                "type": "number",
                "meaning": "Fee amount in the system's stored minor units or internal price format."
              },
              "id": {
                "type": "string",
                "meaning": "Internal fee entry identifier."
              },
              "type": {
                "type": "string",
                "meaning": "Fee type, for example `areaFee`."
              },
              "i18n": {
                "type": "object",
                "meaning": "Localized fee label by language.",
                "shape": {
                  "<lang>": {
                    "type": "object",
                    "meaning": "Localized fee entry.",
                    "shape": {
                      "name": {
                        "type": "string",
                        "meaning": "Localized fee name."
                      }
                    }
                  }
                }
              }
            }
          }
        }
      },
      "discountWorkTime": {
        "type": "object | array | null",
        "meaning": "Time-based discount schedule configuration, if used."
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
          },
          "active": {
            "type": "boolean",
            "meaning": "Whether automatic order acceptance is enabled."
          }
        }
      },
      "defaultTips": {
        "type": "number | null",
        "meaning": "Default tip value offered in this flow, if configured."
      },
      "tipsSettings": {
        "type": "object",
        "meaning": "Detailed tip configuration object. Can be empty if defaults are used."
      }
    }
  },
  "createdAt": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "tablePayments object creation timestamp."
  },
  "default": {
    "type": "boolean",
    "meaning": "Whether this is the default area/channel."
  },
  "emails": {
    "type": "array | object | null",
    "meaning": "Email-related configuration for notifications or routing, if used."
  },
  "guid": {
    "type": "string",
    "meaning": "Public or shareable short identifier used in links or routing."
  },
  "i18n": {
    "type": "object",
    "meaning": "Localized area-level content by language.",
    "shape": {
      "<lang>": {
        "type": "object",
        "meaning": "Localized tablePayments entry.",
        "shape": {
          "name": {
            "type": "string",
            "meaning": "Localized display name."
          },
          "description": {
            "type": "string",
            "meaning": "Localized description, if supported. Can be absent or empty."
          }
        }
      }
    }
  },
  "itemCount": {
    "type": "number",
    "meaning": "Total number of items in the area/menu, likely stored or cached."
  },
  "items": {
    "type": "array",
    "meaning": "List of menu items available in this area.",
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
    "meaning": "Public link for the area/channel."
  },
  "paymentMethods": {
    "type": "object",
    "meaning": "Enabled payment methods for this area.",
    "shape": {
      "<paymentMethod>": {
        "type": "boolean",
        "meaning": "Whether the given payment method is enabled."
      },
      "card": {
        "type": "boolean",
        "meaning": "Whether card payment is allowed."
      },
      "cash": {
        "type": "boolean",
        "meaning": "Whether cash payment is allowed."
      },
      "hotel_room": {
        "type": "boolean",
        "meaning": "Whether payment to a hotel room is allowed."
      },
      "online": {
        "type": "boolean",
        "meaning": "Whether online payment is allowed."
      }
    }
  },
  "position": {
    "type": "number",
    "meaning": "Sort order of this area/channel among other menus or areas."
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
    "meaning": "Area/channel type. For this object the value may differ from `takeaway`, for example `simple`."
  },
  "updatedAt": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "tablePayments object update timestamp."
  },
  "hideFromTableReservation": {
    "type": "boolean",
    "meaning": "Whether this area should be hidden from table reservation flows."
  },
  "posSync": {
    "type": "object",
    "meaning": "POS synchronization metadata for the tablePayments object.",
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
        "meaning": "POS sync schema/version number, if present."
      }
    }
  },
  "location_points": {
    "type": "array",
    "meaning": "Physical tables, seats, points, or service locations assigned to this area.",
    "itemShape": {
      "_id": {
        "type": "string",
        "meaning": "Internal location point identifier."
      },
      "active": {
        "type": "boolean",
        "meaning": "Location point active state."
      },
      "area": {
        "type": "string",
        "meaning": "Reference to the parent `tablePayments._id` area."
      },
      "createdAt": {
        "type": "string",
        "format": "ISO datetime",
        "meaning": "Location point creation timestamp."
      },
      "guid": {
        "type": "string",
        "meaning": "Public or shareable short identifier for the location point."
      },
      "i18n": {
        "type": "object",
        "meaning": "Localized location point content by language.",
        "shape": {
          "<lang>": {
            "type": "object",
            "meaning": "Localized location point entry.",
            "shape": {
              "name": {
                "type": "string",
                "meaning": "Localized location point name, such as table name or point label."
              }
            }
          }
        }
      },
      "link": {
        "type": "string",
        "meaning": "Public link assigned to the location point."
      },
      "posID": {
        "type": "string",
        "meaning": "External POS identifier for the location point, if synced."
      },
      "position": {
        "type": "number",
        "meaning": "Sort order of the location point within the area."
      },
      "seats": {
        "type": "number",
        "meaning": "Number of seats assigned to the location point."
      },
      "updatedAt": {
        "type": "string",
        "format": "ISO datetime",
        "meaning": "Location point update timestamp."
      },
      "hideFromTableReservation": {
        "type": "boolean",
        "meaning": "Whether this specific location point should be hidden from table reservation flows."
      }
    }
  }
}
```

