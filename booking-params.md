# Booking params

JSON-shaped reference for the `Booking params` object.

Notes:

- This file intentionally mirrors the API response structure.
- It uses `jsonc` style so descriptions can live next to fields.
- This object controls reservation behavior, timing limits, customer messaging, media, deposit settings, location selection, and guest markers.
- Editing happens in the admin booking screen, which composes several sections: a basic-settings form, an automation form, an advanced-settings form, a working-hours-exceptions editor, a styling block, a deposit block, a guest-markers block, and a customer-message (comment) block.
- Whole booking screen is gated by the `booking` company feature flag and the `booking` client-feature; without those, the editor is hidden entirely.
- On save, the payload is stripped of `media`, `createdAt`, and `updatedAt` (those fields are server-managed). `workingHours.exceptions` is updated through a separate save action.
- Form-level validations enforced before save:
  - `1 <= maxPeopleCount <= 1000`
  - `minVisitDuration <= visitDuration <= maxVisitDuration`
  - `minIntervalBeforeClosed >= minVisitDuration`

```jsonc
{
  "allowRequestOutsideWorkingHours": {
    "type": "boolean",
    "default": false,
    "behavior": "When true, customers may submit booking requests outside the configured working hours. Unlocks `showWorkingHoursMessage` (which is hidden until this flag is true).",
    "meaning": "Whether booking requests are allowed outside configured working hours.",
  },
  "autoCancel": {
    "type": "object",
    "default": { "enabled": true, "interval": 200 },
    "behavior": "When `enabled` is true, unconfirmed booking requests are auto-cancelled after `interval` minutes. The interval picker is only editable while `enabled` is true.",
    "shape": {
      "enabled": {
        "type": "boolean",
        "meaning": "Whether automatic cancellation is enabled.",
      },
      "interval": {
        "type": "number",
        "unit": "minutes",
        "options": "30..720 in 30-minute steps",
        "meaning": "Auto-cancellation interval in minutes.",
      },
    },
  },
  "autoConfirm": {
    "type": "object",
    "default": {
      "enabled": false,
      "type": "byTables",
      "zoneFullFillPercent": 60,
      "visitDuration": 120,
      "visitorsLimit": 4,
    },
    "behavior": "When `enabled` is true, bookings are auto-confirmed by occupancy rules. All sub-fields (`type`, `zoneFullFillPercent`, `visitorsLimit`, `visitDuration`) are only editable while `enabled` is true.",
    "shape": {
      "enabled": {
        "type": "boolean",
        "meaning": "Whether auto-confirm is on.",
      },
      "type": {
        "type": "string",
        "values": ["byTables", "bySeats"],
        "meaning": "Occupancy strategy: by physical tables or by seats.",
      },
      "zoneFullFillPercent": {
        "type": "number",
        "unit": "%",
        "options": [10, 20, 30, 40, 50, 60, 70, 80, 90, 100],
        "meaning": "Occupancy threshold above which auto-confirm stops accepting.",
      },
      "visitorsLimit": {
        "type": "number",
        "options": "1..10",
        "meaning": "Maximum guests in a single booking eligible for auto-confirm.",
      },
      "visitDuration": {
        "type": "number",
        "unit": "minutes",
        "options": "60..360 in 30-minute steps",
        "meaning": "Visit duration assumed by auto-confirm logic.",
      },
    },
  },
  "blockRequestWhenFullyOccupied": {
    "type": "boolean",
    "behavior": "When true, the booking request endpoint refuses new requests once configured occupancy is reached. Toggle in the advanced-settings section.",
    "meaning": "Whether new booking requests are blocked when occupancy is full.",
  },
  "createdAt": {
    "type": "string",
    "format": "ISO datetime",
    "behavior": "Server-managed. Stripped from the payload before save.",
    "meaning": "Booking params creation timestamp.",
  },
  "customerMessage": {
    "type": "object",
    "behavior": "Optional localized message shown to customers in the booking widget. Edited in the dedicated comment/message block on the booking screen. Toggling the message off clears `customerMessage` entirely.",
    "shape": {
      "i18n": {
        "type": "object",
        "meaning": "Localization map: { [lang]: { name: string } }. Language keys come from the supported set (e.g. `en`, `uk`, `cz`, `de`, `es`, `fr`, `it`, `nl`, `pl`, `ru`, `sk`, `tr`, `hu`, `zh`, `kk`, `et`, `lt`, `lv`, `ge`, `ro`, `da`, `fi`, `no`, `sv`).",
      },
    },
  },
  "deposit": {
    "type": "object",
    "default": {
      "active": false,
      "amount": 100,
      "currency": "<company.currency>",
      "perPerson": false,
      "minPersonsCount": null,
    },
    "behavior": "The deposit block is only visible if the company has `onlinePayment` enabled. `currency` is always overwritten with `company.currency` on load. When `schedule` is set, the fixed `amount` field is hidden in favour of the schedule editor; otherwise `schedule` is null. Toggling `minPersonsCount` off sets it to null; toggling on sets it to 1. Sub-fields are only visible while `active` is true.",
    "shape": {
      "active": {
        "type": "boolean",
        "meaning": "Whether a booking deposit is required.",
      },
      "amount": {
        "type": "number",
        "unit": "<deposit.currency>",
        "meaning": "Deposit amount when no schedule is set.",
      },
      "currency": {
        "type": "string",
        "meaning": "Deposit currency code such as `USD`, `EUR`, `CZK`.",
      },
      "perPerson": {
        "type": "boolean",
        "meaning": "If true, deposit amount is multiplied by guest count.",
      },
      "minPersonsCount": {
        "type": "number | null",
        "meaning": "Minimum guest count required before deposit rules apply. null disables the threshold.",
      },
      "schedule": {
        "type": "object | null",
        "meaning": "When set, replaces the flat `amount` with a recurring/dated schedule.",
        "shape": {
          "recurring": {
            "type": "array",
            "meaning": "Recurring weekly time-windows with their own deposit amount.",
            "itemShape": {
              "dayOfWeek": {
                "type": "number",
                "values": [0, 1, 2, 3, 4, 5, 6],
                "meaning": "Day index, 0..6.",
              },
              "from": {
                "type": "string",
                "format": "HH:mm",
                "meaning": "Window start time.",
              },
              "till": {
                "type": "string",
                "format": "HH:mm",
                "meaning": "Window end time.",
              },
              "amount": {
                "type": "number",
                "meaning": "Deposit amount for this window.",
              },
            },
          },
          "dates": {
            "type": "array",
            "meaning": "Specific calendar-date windows with their own amount.",
            "itemShape": {
              "date": {
                "type": "string",
                "format": "ISO date",
                "meaning": "Calendar date.",
              },
              "from": {
                "type": "string",
                "format": "HH:mm",
                "meaning": "Window start time.",
              },
              "till": {
                "type": "string",
                "format": "HH:mm",
                "meaning": "Window end time.",
              },
              "amount": {
                "type": "number",
                "meaning": "Deposit amount for this window.",
              },
            },
          },
        },
      },
    },
  },
  "emailNotifications": {
    "type": "boolean",
    "behavior": "Not surfaced in the booking-params form; controlled elsewhere (e.g. notification center) or set by server defaults.",
    "meaning": "Whether booking-related email notifications are sent.",
  },
  "emailReminder": {
    "type": "object",
    "default": { "active": false },
    "behavior": "Only the `active` toggle is editable from the booking-params form; `timeValue` is set elsewhere or kept at the server default.",
    "shape": {
      "active": {
        "type": "boolean",
        "meaning": "Whether email reminders are enabled.",
      },
      "timeValue": {
        "type": "number",
        "unit": "minutes",
        "meaning": "Reminder lead time before booking.",
      },
    },
  },
  "enabled": {
    "type": "boolean",
    "behavior": "Top-level booking module switch. Note that page-level visibility is also gated by the `booking` company feature flag and the `booking` client-feature, not solely by this field.",
    "meaning": "Whether the booking feature is enabled for this venue.",
  },
  "guestMarkerSettings": {
    "type": "object | null",
    "default": null,
    "behavior": "Optional guest tags. Hard limit of 6 entries — once `list.length >= 6`, the add button is disabled. Toggling the section off keeps the list and flips `active` to false; explicitly passing null clears the whole object.",
    "shape": {
      "active": {
        "type": "boolean",
        "meaning": "Whether guest markers are enabled.",
      },
      "list": {
        "type": "array",
        "constraint": "max 6 items",
        "meaning": "List of guest markers. Each entry is a localization map: { [lang]: { name: string } }.",
        "itemShape": {
          "<lang>": {
            "type": "object",
            "shape": {
              "name": {
                "type": "string",
                "constraint": "max 40 characters",
                "meaning": "Localized guest marker label.",
              },
            },
          },
        },
      },
    },
  },
  "interval": {
    "type": "number",
    "behavior": "Not surfaced in the booking-params form. Server-managed slot interval used by booking logic.",
    "meaning": "Booking slot interval, most likely in minutes.",
  },
  "locationSelector": {
    "type": "boolean",
    "default": false,
    "behavior": "When true, the customer can pick a booking area on the booking widget and the area-picker checkboxes are revealed in the admin form. Picker lists `areas` filtered by `simple` area type.",
    "meaning": "Whether the customer can choose a booking location/area.",
  },
  "locationSelectorList": {
    "type": "array",
    "default": [],
    "behavior": "List of allowed area `_id`s. Editable only while `locationSelector` is true; toggling each area checkbox adds/removes it from the list.",
    "itemShape": "string  // area._id",
  },
  "maxDaysBefore": {
    "type": "number",
    "unit": "days",
    "default": 30,
    "constraint": "<= 200",
    "behavior": "Maximum days in advance a customer can place a booking. Hard cap of 200 enforced by the input.",
    "meaning": "Maximum number of days in advance that a booking can be created.",
  },
  "maxPeopleCount": {
    "type": "number",
    "default": 10,
    "constraint": "1..1000",
    "behavior": "Validated on save: rejects with an error if outside the 1..1000 range.",
    "meaning": "Maximum number of people allowed in a single booking.",
  },
  "maxVisitDuration": {
    "type": "number",
    "unit": "minutes",
    "default": 120,
    "options": "30..300 in 30-minute steps (first step hidden)",
    "behavior": "Validated on save: must be `>= visitDuration`.",
    "meaning": "Maximum visit duration allowed for a booking.",
  },
  "media": {
    "type": "object | null",
    "behavior": "Booking media (cover/banner). Read-only from the booking-params form — explicitly stripped from the payload before save.",
    "shape": {
      "_id": {
        "type": "string",
        "meaning": "Internal media identifier.",
      },
      "name": {
        "type": "string",
        "meaning": "Internal asset name.",
      },
      "url": {
        "type": "string",
        "meaning": "Public or CDN URL of the booking media asset.",
      },
      "originalFile": {
        "type": "object",
        "shape": {
          "url": {
            "type": "string",
            "meaning": "Public or CDN URL of the original uploaded file.",
          },
          "ext": {
            "type": "string",
            "meaning": "Original file extension.",
          },
        },
      },
      "webp": {
        "type": "object",
        "meaning": "WebP version of the booking media asset.",
        "shape": {
          "url": {
            "type": "string",
            "meaning": "Public or CDN URL of the WebP asset.",
          },
        },
      },
    },
  },
  "minIntervalBefore": {
    "type": "number",
    "unit": "minutes",
    "options": "15, 30, then 30..720 in 30-minute steps",
    "default": 200,
    "behavior": "Minimum lead time required before the booking start time.",
    "meaning": "Minimum time required before the booking start time.",
  },
  "minIntervalBeforeClosed": {
    "type": "number",
    "unit": "minutes",
    "options": "30..360 in 30-minute steps",
    "default": 0,
    "behavior": "Validated on save: must be `>= minVisitDuration`.",
    "meaning": "Minimum time required before closing time for accepting a booking.",
  },
  "minVisitDuration": {
    "type": "number",
    "unit": "minutes",
    "default": 120,
    "options": "30..300 in 30-minute steps",
    "behavior": "Validated on save: must be `<= visitDuration`.",
    "meaning": "Minimum visit duration allowed for a booking.",
  },
  "requiredEmailBooking": {
    "type": "boolean",
    "behavior": "Toggle in the basic-settings section.",
    "meaning": "Whether customer email is required during booking.",
  },
  "showWorkingHoursMessage": {
    "type": "boolean",
    "default": false,
    "behavior": "Only editable while `allowRequestOutsideWorkingHours` is true; the toggle is hidden otherwise.",
    "meaning": "Whether a working-hours-related message is shown in the booking flow.",
  },
  "updatedAt": {
    "type": "string",
    "format": "ISO datetime",
    "behavior": "Server-managed. Stripped from the payload before save.",
    "meaning": "Booking params update timestamp.",
  },
  "visitDuration": {
    "type": "number",
    "unit": "minutes",
    "default": 30,
    "options": "30..240 in 30-minute steps",
    "behavior": "Validated on save: must satisfy `minVisitDuration <= visitDuration <= maxVisitDuration`.",
    "meaning": "Default booking visit duration.",
  },
  "workingHours": {
    "type": "object",
    "behavior": "Edited in a separate working-hours-exceptions editor (not in the main booking-params form). Its save action sends the whole booking-params object plus the new `exceptions` list.",
    "shape": {
      "exceptions": {
        "type": "array",
        "meaning": "Date-specific overrides of working hours.",
        "itemShape": {
          "date": {
            "type": "string",
            "format": "ISO date",
            "meaning": "Exception date.",
          },
          "from": {
            "type": "string",
            "format": "HH:mm",
            "default": "00:00",
            "meaning": "Override opening time. Defaults to `00:00` when an exception is added.",
          },
          "till": {
            "type": "string",
            "format": "HH:mm",
            "default": "24:00",
            "meaning": "Override closing time. Defaults to `24:00` when an exception is added.",
          },
        },
      },
    },
  },
  "dbName": {
    "type": "string",
    "behavior": "Server-injected tenant identifier. Not editable from the admin app.",
    "meaning": "Technical tenant or database identifier.",
  },
}
```
