# Delivery

JSON-shaped reference for the `delivery` object.

Notes:
- This file intentionally mirrors the API response structure.
- It uses `jsonc` style so descriptions can live next to fields.
- Relations between `sections`, `categories`, and `items` are preserved through ID references.
- The overall menu structure is similar to `takeaway`, but this object also includes delivery zones, delivery pricing, address behavior, and courier-related ordering rules.

```jsonc
{
  "_id": {
    "type": "string",
    "meaning": "Internal delivery configuration identifier."
  },
  "active": {
    "type": "boolean",
    "meaning": "Global active state of the delivery channel."
  },
  "categories": {
    "type": "array",
    "meaning": "List of delivery categories. Categories usually belong to sections and group items.",
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
    "meaning": "Delivery channel configuration: delivery zones, pricing, schedule, discounts, address rules, tips, and order handling.",
    "shape": {
      "distances": {
        "type": "array",
        "meaning": "Distance-based delivery pricing rules, usually grouped by service radius.",
        "itemShape": {
          "deliveryCost": {
            "type": "array",
            "meaning": "Pricing rules for this distance band.",
            "itemShape": {
              "orderAmount": {
                "type": "number",
                "meaning": "Order amount threshold used for this delivery pricing rule."
              },
              "deliveryAmount": {
                "type": "number",
                "meaning": "Delivery fee amount in the system's stored minor units or internal price format."
              },
              "costProperties": {
                "type": "object",
                "meaning": "Extra pricing behavior metadata for this rule.",
                "shape": {
                  "type": {
                    "type": "string",
                    "meaning": "Pricing mode, likely enum-like values such as `DEFINED` or `UNDEFINED`."
                  },
                  "i18n": {
                    "type": "object",
                    "meaning": "Localized explanation for special pricing behavior, if applicable.",
                    "shape": {
                      "<lang>": {
                        "type": "object",
                        "meaning": "Localized costProperties entry.",
                        "shape": {
                          "description": {
                            "type": "string",
                            "meaning": "Localized explanation text shown to the customer."
                          },
                          "name": {
                            "type": "string",
                            "meaning": "Localized title or label for the pricing rule."
                          }
                        }
                      }
                    }
                  }
                }
              },
              "posID": {
                "type": "string",
                "meaning": "External POS identifier for the pricing rule, if synced."
              }
            }
          },
          "radius": {
            "type": "number",
            "meaning": "Maximum distance radius for this pricing band."
          },
          "enabled": {
            "type": "boolean",
            "meaning": "Whether this distance rule is active."
          }
        }
      },
      "polygons": {
        "type": "array",
        "meaning": "Polygon-based delivery zones, if polygon coverage is used instead of or in addition to radius rules."
      },
      "fixed": {
        "type": "array",
        "meaning": "Fixed delivery pricing rules or fixed delivery zones, if used."
      },
      "deliveryCost": {
        "type": "array",
        "meaning": "Fallback or legacy delivery cost rules, if used separately from distance/polygon/fixed strategies."
      },
      "workTime": {
        "type": "array",
        "meaning": "Delivery-specific working hours. Usually overrides or specializes general company working hours for the delivery channel.",
        "itemShape": {
          "dayOfWeek": {
            "type": "number",
            "meaning": "Day index, usually `0..6`. Exact mapping should be confirmed in product logic."
          },
          "active": {
            "type": "boolean",
            "meaning": "Whether delivery accepts orders on that day."
          },
          "from": {
            "type": "string",
            "meaning": "Opening time for delivery orders."
          },
          "till": {
            "type": "string",
            "meaning": "Closing time for delivery orders."
          }
        }
      },
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
      "time": {
        "type": "object",
        "meaning": "Delivery ordering time settings.",
        "shape": {
          "allowASAP": {
            "type": "boolean",
            "meaning": "Allows ASAP delivery orders."
          },
          "allowTime": {
            "type": "boolean",
            "meaning": "Allows choosing a scheduled delivery time."
          },
          "delay": {
            "type": "number",
            "meaning": "Default lead time before the earliest available delivery slot."
          },
          "interval": {
            "type": "number",
            "meaning": "Time slot interval step."
          },
          "preOrderSkipDelay": {
            "type": "boolean",
            "meaning": "Whether preorder flow can skip standard delay rules."
          },
          "allowNonWorkingOrder": {
            "type": "boolean",
            "meaning": "Allows placing delivery orders outside configured working hours."
          },
          "minPreOrdersDayCount": {
            "type": "number",
            "meaning": "Minimum number of days ahead required for preorder."
          },
          "maxPreOrdersDayCount": {
            "type": "number",
            "meaning": "Maximum number of days ahead available for preorder."
          }
        }
      },
      "type": {
        "type": "string",
        "meaning": "Delivery zone strategy type, likely enum-like values such as `radius`, and possibly polygon/fixed-based modes."
      },
      "strictMatch": {
        "type": "boolean",
        "meaning": "Whether address matching must strictly fit a configured delivery rule."
      },
      "toDoor": {
        "type": "object",
        "meaning": "Door-delivery behavior configuration.",
        "shape": {
          "active": {
            "type": "boolean",
            "meaning": "Whether door delivery is enabled."
          },
          "forcePublic": {
            "type": "boolean",
            "meaning": "Whether door-delivery option is always publicly visible or enforced."
          }
        }
      },
      "hideFloorApartmentField": {
        "type": "boolean",
        "meaning": "Whether floor/apartment address fields are hidden in checkout."
      },
      "allowNoStreetNumber": {
        "type": "boolean",
        "meaning": "Whether orders are allowed without a street number in the delivery address."
      },
      "description": {
        "type": "string",
        "meaning": "Delivery-specific description or customer-facing informational text."
      },
      "defaultTips": {
        "type": "number | null",
        "meaning": "Default tip value offered in delivery flow."
      },
      "tipsSettings": {
        "type": "object",
        "meaning": "Detailed tip configuration.",
        "shape": {
          "defaultTipsType": {
            "type": "string",
            "meaning": "Tip requirement/default mode, likely enum-like values such as `REQUIRED`."
          },
          "recipient": {
            "type": "string",
            "meaning": "Who receives the tips, for example `restaurant`."
          },
          "recipientAccount": {
            "type": "string",
            "meaning": "Internal account or balance destination for tips."
          }
        }
      },
      "additionalFees": {
        "type": "object | null",
        "meaning": "Additional fee configuration applied to delivery orders.",
        "shape": {
          "items": {
            "type": "array",
            "meaning": "Configured extra fee entries.",
            "itemShape": {
              "amount": {
                "type": "number",
                "meaning": "Fee amount in the system's stored minor units or internal price format."
              },
              "posID": {
                "type": "string",
                "meaning": "External POS identifier for the fee item, if synced."
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
      "menuSync": {
        "type": "boolean",
        "meaning": "Whether delivery menu is synchronized with another source, likely POS or shared menu."
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
          }
        }
      },
      "orderPhoneVerification": {
        "type": "boolean",
        "meaning": "Whether phone verification is required during delivery ordering."
      }
    }
  },
  "createdAt": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "Delivery object creation timestamp."
  },
  "default": {
    "type": "boolean",
    "meaning": "Whether this is the default channel/menu."
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
    "meaning": "Localized delivery-level content by language.",
    "shape": {
      "<lang>": {
        "type": "object",
        "meaning": "Localized delivery object entry.",
        "shape": {
          "name": {
            "type": "string",
            "meaning": "Localized delivery display name."
          },
          "description": {
            "type": "string",
            "meaning": "Localized delivery description, if supported. Can be absent or empty."
          }
        }
      }
    }
  },
  "items": {
    "type": "array",
    "meaning": "List of menu items available in delivery.",
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
    "meaning": "Public delivery link."
  },
  "paymentMethods": {
    "type": "object",
    "meaning": "Enabled payment methods for delivery.",
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
      "bill": {
        "type": "boolean",
        "meaning": "Whether billing or invoice-style payment is allowed."
      }
    }
  },
  "posID": {
    "type": "string",
    "meaning": "External POS identifier for the delivery channel itself."
  },
  "position": {
    "type": "number",
    "meaning": "Delivery sort order among other channels or menus."
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
    "meaning": "Channel type. For this object it is expected to be `delivery`."
  },
  "updatedAt": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "Delivery object update timestamp."
  },
  "posSync": {
    "type": "object",
    "meaning": "POS synchronization metadata for the delivery object.",
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
    "meaning": "Location points related to delivery fulfillment or channel binding. Can be empty.",
    "itemShape": "object"
  }
}
```

