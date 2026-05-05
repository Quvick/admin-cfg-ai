# pos

JSON-shaped reference for the `pos` configuration object.

Notes:

- This file mirrors the POS configuration payload (NOT the generic API error envelope). The shape models the in-app `PosDataType`.
- It uses `jsonc` style so descriptions can live next to fields.
- This object describes which POS the venue is integrated with (`provider`) plus a provider-specific bag (`posData`) with credentials, mappings, and behavior flags. Many `posData.*` fields are only relevant to a subset of providers; the editor renders different forms per provider.
- Editing happens on the POS feature page. The whole page is gated by the `pos` company feature flag (and the optional `posSettings` flag for the second-pass settings form).
- The page renders two forms: a connection form (provider, credentials) and a settings form (per-provider behavior, payment mapping). The settings form has its own `validationSettings`.
- Validation rules at save time:
  - Connection form requires the credentials specific to the chosen provider:
    - `storyous`: `posData.merchantId`, `posData.placeId`
    - `poster`: `posData.providerAccountId`
    - `iiko`: `posData.authCode`
  - Settings form requires:
    - `posData.branchId` for every provider except `storyous` and `open-api`
    - `posData.organizationId` for `iiko`
    - `posData.paymentsMapper.platform.cash | card | online` for `poster`, `rkeeper`, `id-pos`
    - When the integration includes a marketplace and `posData.sendPaymentToPos !== false`, the marketplace payment mapping is required for `iiko` and `poster`.
- Several fields are stored as JSON-encoded strings (e.g. `paymentsMapper`, `orderTypesMapper`, `salesTypesMapper`, `usersMapper`, `paymentMethodsMatch`, `priceCategories`, `externalMenus`, `branches`, `paymentMethods`, `tablets`, `employees`, `priceTypes`). The form parses/serialises these on the boundary.
- Sync state lives on `menuSyncStatus` (e.g. `process` while a sync is in flight); the UI shows a non-blocking info message while syncing.

```jsonc
{
  "active": {
    "type": "boolean",
    "behavior": "Master switch for the integration. Toggling off goes through a confirmation dialog and disables provider-specific behavior server-side.",
    "meaning": "Whether the POS integration is currently enabled."
  },
  "bookingSupport": {
    "type": "boolean",
    "behavior": "Server-derived top-level flag mirroring whether the chosen provider supports bookings. Per-integration override at `posData.bookingSupport`.",
    "meaning": "Whether the POS supports bookings."
  },
  "forceNotify": {
    "type": "boolean",
    "behavior": "When true, forces notifications/handshakes to the POS even if the standard flow would skip them.",
    "meaning": "Force-notify flag."
  },
  "fullIntegration": {
    "type": "boolean",
    "behavior": "Top-level flag indicating the integration runs in 'full integration' mode (deeper sync). Per-integration override at `posData.fullIntegration`.",
    "meaning": "Top-level full-integration flag."
  },
  "menuSyncStatus": {
    "type": "string",
    "values": ["idle", "process", "..."],
    "behavior": "Server-managed. While value is `process`, the UI shows a sync-in-progress message; the sync action is also gated to avoid concurrent triggers.",
    "meaning": "Current state of the menu sync process."
  },
  "provider": {
    "type": "string",
    "values": [
      "null",
      "storyous",
      "dotykacka",
      "poster",
      "rkeeper",
      "iiko",
      "open-api",
      "smart-pos",
      "id-pos"
    ],
    "default": "null",
    "behavior": "Selects which provider-specific sub-form is rendered, which `posData.*` fields are required, and which payment-mapping rules apply. `null` means no integration.",
    "meaning": "POS provider identifier."
  },
  "posData": {
    "type": "object",
    "behavior": "Provider-specific bag. The set of meaningful keys depends on `provider`. Many keys are JSON-encoded strings managed via flat keys in the form.",
    "shape": {
      "showForSite": {
        "type": "boolean",
        "behavior": "[rkeeper] Whether to expose the integration on the public site.",
        "meaning": "Public visibility of the integration."
      },
      "createDraftOrders": {
        "type": "boolean",
        "behavior": "[iiko] Create orders as drafts in the POS instead of fully placing them.",
        "meaning": "Whether the POS should receive orders as drafts."
      },
      "useExternalMenu": {
        "type": "string",
        "behavior": "[iiko] Selected external menu identifier.",
        "meaning": "External menu selection."
      },
      "priceCategoryId": {
        "type": "string",
        "behavior": "[iiko] Selected price category identifier.",
        "meaning": "Active price category."
      },
      "priceCategories": {
        "type": "string",
        "behavior": "[iiko] JSON-encoded list of available price categories.",
        "meaning": "Source list of price categories."
      },
      "externalMenuId": {
        "type": "string",
        "behavior": "[iiko] Selected external menu id.",
        "meaning": "Active external menu."
      },
      "externalMenus": {
        "type": "string",
        "behavior": "[iiko] JSON-encoded list of available external menus.",
        "meaning": "Source list of external menus."
      },
      "customerVATSupport": {
        "type": "boolean",
        "behavior": "[dotykacka] Whether to pass customer VAT info to the POS.",
        "meaning": "Customer VAT support."
      },
      "autoConfirm": {
        "type": "boolean",
        "behavior": "[iiko] Whether incoming orders are auto-confirmed in the POS.",
        "meaning": "Auto-confirm orders."
      },
      "courierId": {
        "type": "string",
        "behavior": "[iiko] Default courier id used when sending delivery orders to the POS.",
        "meaning": "Courier identifier."
      },
      "salesTypesMapper": {
        "type": "string",
        "behavior": "[id-pos, when `orderTypeByUser` is false] JSON-encoded mapping of order types (`takeaway`, `delivery`, `table`) to POS sales-type ids. Edited via flat keys `posData.salesTypesMapper.order.<orderType>`.",
        "meaning": "Mapping order types → POS sales types."
      },
      "usersMapper": {
        "type": "string",
        "behavior": "[id-pos, when `orderTypeByUser` is true] JSON-encoded mapping of order types to POS user ids. Edited via flat keys `posData.usersMapper.order.<orderType>`.",
        "meaning": "Mapping order types → POS users."
      },
      "userId": {
        "type": "string",
        "behavior": "[dotykacka, id-pos] User identifier used for outbound POS calls.",
        "meaning": "Active POS user id."
      },
      "priceListId": {
        "type": "string",
        "behavior": "[id-pos] Price list identifier.",
        "meaning": "Active price list."
      },
      "tipId": {
        "type": "string",
        "behavior": "[id-pos] POS id of the tip line item.",
        "meaning": "Tip product id."
      },
      "posDeviceId": {
        "type": "number",
        "behavior": "[id-pos] POS device identifier.",
        "meaning": "POS device id."
      },
      "apiKey": {
        "type": "string",
        "behavior": "[id-pos] API key for outbound calls.",
        "meaning": "API key."
      },
      "clientId": {
        "type": "string",
        "behavior": "[id-pos] Client identifier.",
        "meaning": "Client id."
      },
      "accessToken": {
        "type": "string",
        "behavior": "[poster] Access token. Stored as null when empty.",
        "meaning": "Access token."
      },
      "branchId": {
        "type": "string",
        "behavior": "Required for every provider except `storyous` and `open-api`. Selected from the list provided by `branches`.",
        "meaning": "Active POS branch identifier."
      },
      "branches": {
        "type": "string",
        "behavior": "JSON-encoded list of available branches; source for the `branchId` selector.",
        "meaning": "Source list of branches."
      },
      "QRPaymentsPOSIntegrationOnly": {
        "type": "boolean | string",
        "behavior": "When true, QR payments are only allowed when the POS integration is active.",
        "meaning": "Restrict QR payments to POS-integrated mode."
      },
      "autoCloseOrder": {
        "type": "boolean",
        "behavior": "[rkeeper] Whether the POS auto-closes orders.",
        "meaning": "Auto-close orders."
      },
      "marketplaceSkipFoodoraCashMethod": {
        "type": "boolean",
        "behavior": "[dotykacka] Skip the Foodora cash payment method when sending payments.",
        "meaning": "Foodora cash skip."
      },
      "paymentMethodId": {
        "type": "number",
        "behavior": "[poster] Default payment method id used as a fallback for cash/card/online when no specific mapping exists.",
        "meaning": "Default POS payment method id."
      },
      "paymentMethods": {
        "type": "string",
        "behavior": "JSON-encoded list of available payment methods, used to populate selectors.",
        "meaning": "Source list of payment methods."
      },
      "tablets": {
        "type": "string",
        "behavior": "[poster] JSON-encoded list of registered tablets, used by `tabletId`.",
        "meaning": "Source list of tablets."
      },
      "employees": {
        "type": "string",
        "behavior": "[poster] JSON-encoded list of employees, used by `employeeId`.",
        "meaning": "Source list of employees."
      },
      "providerAccountId": {
        "type": "string",
        "behavior": "[poster] Required. Provider's account/tenant identifier.",
        "meaning": "Provider account id."
      },
      "autoClose": {
        "type": "boolean | null",
        "default": "true",
        "behavior": "[storyous, dotykacka, iiko, id-pos] Whether the POS auto-closes orders. Defaults to true unless explicitly false (storyous/dotykacka).",
        "meaning": "Auto-close behavior."
      },
      "pluginIgnoreStopList": {
        "type": "boolean | null",
        "behavior": "[iiko] Plugin behavior — whether to ignore stop-list entries when sending orders.",
        "meaning": "Ignore stop-list."
      },
      "sendReceiptTechData": {
        "type": "boolean | null",
        "behavior": "[iiko] Whether to attach technical data to receipts.",
        "meaning": "Send receipt tech data."
      },
      "addressPrediction": {
        "type": "boolean | null",
        "behavior": "[iiko] Whether to enable address prediction in the POS.",
        "meaning": "Address prediction."
      },
      "groupSameItems": {
        "type": "boolean | null",
        "behavior": "[iiko] Group identical items into a single line.",
        "meaning": "Group same items."
      },
      "fullIntegration": {
        "type": "boolean | null",
        "behavior": "[storyous, dotykacka, rkeeper, iiko, smart-pos, poster, id-pos] Provider-level full-integration switch. Boolean coercion is provider-specific (e.g. poster defaults to true unless explicitly false).",
        "meaning": "Provider-level full-integration flag."
      },
      "sendAcquirerPaymentsData": {
        "type": "boolean",
        "behavior": "[poster] Whether to attach acquirer payment data when sending to the POS.",
        "meaning": "Send acquirer data."
      },
      "combineItemsQuantity": {
        "type": "boolean",
        "behavior": "[id-pos] Combine quantities of identical items in the POS receipt.",
        "meaning": "Combine identical items."
      },
      "visibleForAll": {
        "type": "boolean",
        "behavior": "[id-pos] Whether the integration is visible to all venues in the chain.",
        "meaning": "Visible to all venues."
      },
      "orderTypeByUser": {
        "type": "boolean",
        "behavior": "[id-pos] When true, the order-type → POS-user mapping (`usersMapper`) is used; when false, the order-type → sales-type mapping (`salesTypesMapper`) is used.",
        "meaning": "Order-type-by-user mode."
      },
      "autoCloseOutside": {
        "type": "boolean | null",
        "default": "true",
        "behavior": "[dotykacka] Whether to auto-close orders received outside working hours. Defaults to true unless explicitly false.",
        "meaning": "Auto-close outside working hours."
      },
      "bookingSupport": {
        "type": "boolean | null",
        "behavior": "[dotykacka] Per-integration override of bookings support.",
        "meaning": "Bookings support."
      },
      "key": {
        "type": "string",
        "behavior": "[rkeeper, iiko] Integration key.",
        "meaning": "Integration key."
      },
      "paymentMethodsMatch": {
        "type": "string",
        "behavior": "[rkeeper] JSON-encoded mapping `{ cash, card, online }` of platform payment methods to POS payment ids; used as the seed for `paymentsMapper`.",
        "meaning": "Payment-method match dictionary."
      },
      "rkeeperComment": {
        "type": "string",
        "behavior": "[rkeeper] Free-text comment attached to outbound orders.",
        "meaning": "rkeeper comment."
      },
      "useChoiceButton": {
        "type": "boolean | null",
        "behavior": "[rkeeper] Whether to expose the rkeeper 'choice' button in checkout.",
        "meaning": "Choice button."
      },
      "pluginConnected": {
        "type": "boolean | null",
        "behavior": "[rkeeper, iiko] Server-derived flag indicating whether the local plugin is currently connected.",
        "meaning": "Plugin connection status."
      },
      "organizationId": {
        "type": "string",
        "behavior": "[iiko] Required at settings save. Identifies the iiko organization.",
        "meaning": "iiko organization id."
      },
      "paymentId": {
        "type": "string",
        "behavior": "[iiko] Default platform payment id used as the source for the online payment mapping.",
        "meaning": "Default payment id."
      },
      "paymentCashId": {
        "type": "string",
        "behavior": "[iiko, id-pos] Payment id used for cash payments.",
        "meaning": "Cash payment id."
      },
      "paymentCardId": {
        "type": "string",
        "behavior": "[iiko, id-pos] Payment id used for card payments.",
        "meaning": "Card payment id."
      },
      "tipsPaymentId": {
        "type": "string",
        "behavior": "[iiko] Payment id used for tips.",
        "meaning": "Tips payment id."
      },
      "tipsDishPosID": {
        "type": "string",
        "behavior": "[iiko] POS id of the dish line representing tips.",
        "meaning": "Tips dish id."
      },
      "tipsDishModificatorPosID": {
        "type": "string",
        "behavior": "[iiko] POS id of the modificator used with the tips dish.",
        "meaning": "Tips dish modificator id."
      },
      "discountId": {
        "type": "string",
        "behavior": "[iiko, id-pos] POS id of the discount applied for platform discounts.",
        "meaning": "Discount id."
      },
      "employeeId": {
        "type": "string",
        "behavior": "[poster] Selected employee for outbound orders.",
        "meaning": "Active employee id."
      },
      "tabletId": {
        "type": "string",
        "behavior": "[poster] Selected tablet for outbound orders.",
        "meaning": "Active tablet id."
      },
      "commentPosId": {
        "type": "string",
        "behavior": "[smart-pos] POS id of the comment line.",
        "meaning": "Comment line id."
      },
      "exOrderPrefix": {
        "type": "string",
        "behavior": "[smart-pos] Prefix prepended to external order numbers.",
        "meaning": "External order prefix."
      },
      "tipsPosID": {
        "type": "string",
        "behavior": "[dotykacka, poster] POS id of the tips line item.",
        "meaning": "Tips POS id."
      },
      "roundPosID": {
        "type": "string",
        "behavior": "[dotykacka, rkeeper, poster] POS id of the rounding line item.",
        "meaning": "Rounding POS id."
      },
      "placeId": {
        "type": "string",
        "behavior": "[storyous] Required at connection. Identifies the storyous place.",
        "meaning": "storyous place id."
      },
      "merchantId": {
        "type": "string",
        "behavior": "[storyous] Required at connection. storyous merchant identifier.",
        "meaning": "storyous merchant id."
      },
      "authCode": {
        "type": "string",
        "behavior": "[iiko] Required at connection. Auth code used to bind the integration.",
        "meaning": "Auth code."
      },
      "apiUrl": {
        "type": "string",
        "behavior": "[id-pos] Base URL of the POS API.",
        "meaning": "API URL."
      },
      "usePlugin": {
        "type": "string",
        "behavior": "Connection-form selector indicating whether the integration should connect via plugin or direct API.",
        "meaning": "Plugin selection."
      },
      "remoteId": {
        "type": "string",
        "behavior": "Connection-form remote identifier (provider-dependent meaning).",
        "meaning": "Remote id."
      },
      "courseCode": {
        "type": "string",
        "behavior": "[rkeeper] Course code for outbound orders.",
        "meaning": "Course code."
      },
      "priceTypes": {
        "type": "string",
        "behavior": "[rkeeper] JSON-encoded list of available price types.",
        "meaning": "Source list of price types."
      },
      "printFiscal": {
        "type": "boolean",
        "behavior": "[poster] Whether to print fiscal receipts.",
        "meaning": "Print fiscal."
      },
      "paymentAlreadyFiscalized": {
        "type": "boolean",
        "behavior": "[storyous] Whether the platform considers payments already fiscalised by the POS.",
        "meaning": "Already fiscalised."
      },
      "useMarketplacePaid": {
        "type": "boolean",
        "behavior": "[storyous] Use marketplace-paid logic for orders received from marketplace integrations.",
        "meaning": "Use marketplace-paid."
      },
      "fullIntegrationCloseOrder": {
        "type": "boolean",
        "behavior": "[poster] Auto-close order in full-integration mode.",
        "meaning": "Full-integration close order."
      },
      "tableMergeBills": {
        "type": "boolean",
        "behavior": "[storyous, dotykacka, id-pos] Merge bills for the same table when sending to POS.",
        "meaning": "Table merge bills."
      },
      "usePlatformMenuPrices": {
        "type": "boolean",
        "behavior": "[dotykacka, iiko] Use platform menu prices instead of POS prices for outbound orders.",
        "meaning": "Use platform menu prices."
      },
      "sendPaymentToPos": {
        "type": "boolean",
        "behavior": "[iiko, poster] When false, payment data is not pushed to POS — this also relaxes marketplace mapping requirements.",
        "meaning": "Send payment to POS."
      },
      "passStreet": {
        "type": "boolean",
        "behavior": "[iiko] Pass street info from address as a separate field to POS.",
        "meaning": "Pass street."
      },
      "roundTips": {
        "type": "boolean",
        "behavior": "[iiko] Round tips when forwarding to POS.",
        "meaning": "Round tips."
      },
      "skipTips": {
        "type": "boolean",
        "behavior": "[storyous] Skip forwarding tips to POS.",
        "meaning": "Skip tips."
      },
      "paymentsMapper": {
        "type": "string",
        "behavior": "JSON-encoded mapping. Edited via flat keys: `posData.paymentsMapper.platform.{cash|card|online}` (and per-marketplace keys when applicable). For `poster`, `rkeeper`, `id-pos` the platform.{cash, card, online} keys are required at save.",
        "meaning": "Mapping platform/marketplace payment methods → POS payment ids."
      },
      "orderTypes": {
        "type": "string",
        "behavior": "JSON-encoded list of POS order types.",
        "meaning": "Source list of POS order types."
      },
      "orderTypesMapper": {
        "type": "string",
        "behavior": "[rkeeper, iiko] JSON-encoded mapping. Edited via flat keys `posData.orderTypesMapper.platform.{takeaway|delivery|table}`.",
        "meaning": "Mapping platform order types → POS order types."
      },
      "marketplaceLabel": {
        "type": "string",
        "behavior": "[open-api] Friendly label rendered as `[Open API] <label>`. Defaults to `Open API Provider` when empty.",
        "meaning": "Open API marketplace label."
      },
      "refreshToken": {
        "type": "string",
        "behavior": "Connection refresh token, when applicable to the provider.",
        "meaning": "Refresh token."
      },
      "cloudId": {
        "type": "string",
        "behavior": "Cloud account identifier, when applicable to the provider.",
        "meaning": "Cloud id."
      }
    }
  }
}
```
