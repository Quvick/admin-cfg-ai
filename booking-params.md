# Booking params

JSON-shaped reference for the `Booking params` object.

Notes:
- This file intentionally mirrors the API response structure.
- It uses `jsonc` style so descriptions can live next to fields.
- This object appears to control reservation behavior, timing limits, customer messaging, media, deposit settings, and location selection.

```jsonc
{
  "allowRequestOutsideWorkingHours": {
    "type": "boolean",
    "meaning": "Whether booking requests are allowed outside configured working hours."
  },
  "autoCancel": {
    "type": "object",
    "meaning": "Automatic cancellation behavior for booking requests or reservations.",
    "shape": {
      "enabled": {
        "type": "boolean",
        "meaning": "Whether automatic cancellation is enabled."
      },
      "interval": {
        "type": "number",
        "meaning": "Auto-cancellation interval. Most likely stored in minutes."
      }
    }
  },
  "autoConfirm": {
    "type": "object",
    "meaning": "Automatic confirmation behavior for bookings.",
    "shape": {
      "enabled": {
        "type": "boolean",
        "meaning": "Whether automatic booking confirmation is enabled."
      },
      "zoneFullFillPercent": {
        "type": "number",
        "meaning": "Occupancy threshold percentage used in auto-confirm logic."
      },
      "visitDuration": {
        "type": "number",
        "meaning": "Visit duration used by auto-confirm logic. Most likely stored in minutes."
      },
      "visitorsLimit": {
        "type": "number",
        "meaning": "Maximum number of visitors eligible for auto-confirm."
      },
      "type": {
        "type": "string",
        "meaning": "Auto-confirm strategy type, likely enum-like values such as `byTables`."
      }
    }
  },
  "createdAt": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "Booking params creation timestamp."
  },
  "customerMessage": {
    "type": "object",
    "meaning": "Localized message shown to customers in booking flow.",
    "shape": {
      "i18n": {
        "type": "object",
        "meaning": "Localization map by language code.",
        "shape": {
          "<lang>": {
            "type": "object",
            "meaning": "Localized customer message entry.",
            "shape": {
              "name": {
                "type": "string",
                "meaning": "Customer-facing message text."
              }
            }
          }
        }
      }
    }
  },
  "emailReminder": {
    "type": "object",
    "meaning": "Email reminder behavior for bookings.",
    "shape": {
      "active": {
        "type": "boolean",
        "meaning": "Whether email reminders are enabled."
      },
      "timeValue": {
        "type": "number",
        "meaning": "Reminder lead time before booking. Most likely stored in minutes."
      }
    }
  },
  "maxDaysBefore": {
    "type": "number",
    "meaning": "Maximum number of days in advance that a booking can be created."
  },
  "media": {
    "type": "object | null",
    "meaning": "Booking-related cover or visual asset.",
    "shape": {
      "url": {
        "type": "string",
        "meaning": "Public or CDN URL of the booking media asset."
      },
      "name": {
        "type": "string",
        "meaning": "Internal asset name."
      },
      "webp": {
        "type": "object",
        "meaning": "WebP version of the booking media asset.",
        "shape": {
          "url": {
            "type": "string",
            "meaning": "Public or CDN URL of the WebP asset."
          }
        }
      }
    }
  },
  "minIntervalBefore": {
    "type": "number",
    "meaning": "Minimum time required before the booking start time. Most likely stored in minutes."
  },
  "minIntervalBeforeClosed": {
    "type": "number",
    "meaning": "Minimum time required before closing time for accepting a booking. Most likely stored in minutes."
  },
  "requiredEmailBooking": {
    "type": "boolean",
    "meaning": "Whether customer email is required during booking."
  },
  "showWorkingHoursMessage": {
    "type": "boolean",
    "meaning": "Whether a working-hours-related message is shown in booking flow."
  },
  "updatedAt": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "Booking params update timestamp."
  },
  "visitDuration": {
    "type": "number",
    "meaning": "Default booking visit duration. Most likely stored in minutes."
  },
  "deposit": {
    "type": "object",
    "meaning": "Deposit requirements for bookings.",
    "shape": {
      "active": {
        "type": "boolean",
        "meaning": "Whether a booking deposit is required."
      },
      "amount": {
        "type": "number",
        "meaning": "Deposit amount."
      },
      "currency": {
        "type": "string",
        "meaning": "Deposit currency code such as `USD`, `EUR`, `CZK`."
      },
      "perPerson": {
        "type": "boolean",
        "meaning": "Whether the deposit amount is charged per person."
      },
      "minPersonsCount": {
        "type": "number | null",
        "meaning": "Minimum guest count required before deposit rules apply."
      }
    }
  },
  "locationSelector": {
    "type": "boolean",
    "meaning": "Whether the customer can choose a booking location/area."
  },
  "locationSelectorList": {
    "type": "array",
    "meaning": "List of allowed location or area IDs available in booking selection.",
    "itemShape": "string"
  },
  "blockRequestWhenFullyOccupied": {
    "type": "boolean",
    "meaning": "Whether new booking requests should be blocked when occupancy is full."
  },
  "maxPeopleCount": {
    "type": "number",
    "meaning": "Maximum number of people allowed in a single booking."
  },
  "maxVisitDuration": {
    "type": "number",
    "meaning": "Maximum visit duration allowed for a booking. Most likely stored in minutes."
  },
  "minVisitDuration": {
    "type": "number",
    "meaning": "Minimum visit duration allowed for a booking. Most likely stored in minutes."
  },
  "guestMarkerSettings": {
    "type": "object",
    "meaning": "Optional guest tags or markers that can be attached to a booking.",
    "shape": {
      "active": {
        "type": "boolean",
        "meaning": "Whether guest markers are enabled."
      },
      "list": {
        "type": "array",
        "meaning": "List of available guest markers.",
        "itemShape": {
          "<lang>": {
            "type": "object",
            "meaning": "Localized guest marker entry.",
            "shape": {
              "name": {
                "type": "string",
                "meaning": "Localized guest marker label."
              }
            }
          }
        }
      }
    }
  },
  "dbName": {
    "type": "string",
    "meaning": "Technical tenant or database identifier."
  }
}
```

