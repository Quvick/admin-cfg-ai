# notification config

JSON-shaped reference for the `notification config` (Notification Control Center) object.

Notes:

- This file intentionally mirrors the API response structure.
- It uses `jsonc` style so descriptions can live next to fields.
- The object controls platform-level notifications about feature events: configures email recipients, the Telegram bot transport, and per-feature notification channel toggles.
- Editor lives in the Notification Center admin screen and is gated by both the `notifyControlCenter` company feature flag and a corresponding client-feature; without those, the editor is hidden.
- On every email recipient add/remove, the whole config is saved to the server (no separate "save" step for the recipients list). The bottom feature/transport switches are saved together when the user clicks "Save changes".
- Adding an email recipient runs an email-format validation; invalid input is rejected silently.

```jsonc
{
  "_id": {
    "type": "string",
    "behavior": "Server-managed.",
    "meaning": "Internal notification configuration identifier.",
  },
  "dbName": {
    "type": "string",
    "behavior": "Server-injected tenant identifier. Not editable from the admin app.",
    "meaning": "Technical tenant or database identifier.",
  },
  "mainPlace": {
    "type": "string | null",
    "behavior": "Server-managed reference to the main place/branch of the chain. Not surfaced in the editor.",
    "meaning": "Identifier of the main place this config belongs to in chain setups.",
  },
  "active": {
    "type": "boolean",
    "behavior": "Global on/off for the notification config record.",
    "meaning": "Global active state of the notification configuration.",
  },
  "featureConfig": {
    "type": "object",
    "default": {
      "canOrderDelivery": { "email": false, "telegram": false },
      "canOrderTakeaway": { "email": false, "telegram": false },
      "booking": { "email": false, "telegram": false },
      "marketplace": { "email": false, "telegram": false },
      "posPaymentTable": { "email": false, "telegram": false },
      "marketplacesSyncMenu": { "email": false, "telegram": false },
      "inactiveMenusDailyReport": { "email": false, "telegram": false },
    },
    "behavior": "Per-feature notification channel toggles. Each entry has independent `email` and `telegram` switches. The editor splits keys into two groups: 'feature availability' (canOrderDelivery, canOrderTakeaway, booking, marketplace, posPaymentTable) and 'reporting' (inactiveMenusDailyReport, marketplacesSyncMenu). Saved together via the 'Save changes' button.",
    "shape": {
      "canOrderDelivery": {
        "type": "object",
        "meaning": "Notifications about delivery channel availability.",
        "shape": {
          "email": {
            "type": "boolean",
            "meaning": "Whether to send these notifications via email.",
          },
          "telegram": {
            "type": "boolean",
            "meaning": "Whether to send these notifications via Telegram bot.",
          },
        },
      },
      "canOrderTakeaway": {
        "type": "object",
        "meaning": "Notifications about takeaway channel availability.",
        "shape": {
          "email": { "type": "boolean" },
          "telegram": { "type": "boolean" },
        },
      },
      "booking": {
        "type": "object",
        "meaning": "Notifications about the booking feature.",
        "shape": {
          "email": { "type": "boolean" },
          "telegram": { "type": "boolean" },
        },
      },
      "marketplace": {
        "type": "object",
        "meaning": "Notifications about marketplace integration events.",
        "shape": {
          "email": { "type": "boolean" },
          "telegram": { "type": "boolean" },
        },
      },
      "posPaymentTable": {
        "type": "object",
        "meaning": "Notifications about QR / table payment events.",
        "shape": {
          "email": { "type": "boolean" },
          "telegram": { "type": "boolean" },
        },
      },
      "marketplacesSyncMenu": {
        "type": "object",
        "meaning": "Reporting: marketplace menu-sync results.",
        "shape": {
          "email": { "type": "boolean" },
          "telegram": { "type": "boolean" },
        },
      },
      "inactiveMenusDailyReport": {
        "type": "object",
        "meaning": "Reporting: daily summary of paused/inactive dishes.",
        "shape": {
          "email": { "type": "boolean" },
          "telegram": { "type": "boolean" },
        },
      },
    },
  },
  "transportConfig": {
    "type": "object",
    "default": { "email": { "recipients": [] } },
    "behavior": "Defines the delivery transports for notifications. The Telegram block is server-managed (read-only `link` and `code` used to onboard the bot), while the email recipients list is editable. Adding/removing a recipient persists immediately.",
    "shape": {
      "email": {
        "type": "object",
        "meaning": "Email transport configuration.",
        "shape": {
          "recipients": {
            "type": "array",
            "itemShape": "string  // validated email address",
            "behavior": "Editable list. Each add saves the full config; invalid emails are rejected client-side. Each entry supports a delete action that also saves immediately.",
            "meaning": "List of email recipients receiving the notifications.",
          },
        },
      },
      "telegram": {
        "type": "object",
        "behavior": "Server-provided. The `link` is rendered as a copyable URL and used as the value behind a downloadable QR code; `code` is the activation token used inside the Telegram chat to bind the venue.",
        "shape": {
          "link": {
            "type": "string",
            "meaning": "Telegram bot onboarding/connection link.",
          },
          "code": {
            "type": "string",
            "meaning": "Telegram connection or activation code.",
          },
        },
      },
    },
  },
  "version": {
    "type": "number",
    "behavior": "Server-managed. May be used for migration/backfill compatibility.",
    "meaning": "Configuration schema or object version number.",
  },
}
```
