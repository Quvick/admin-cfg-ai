# Company data

JSON-shaped reference for the `Company data` object.

Notes:

- This file mirrors the API response structure for the company/venue resource.
- It uses `jsonc` style so descriptions can live next to fields.
- Repeated media objects follow the same internal pattern unless stated otherwise.
- Most consumers read the company through a `useCompany` hook; the object is also persisted on the user session and used everywhere as the source of truth for tenant configuration, branding, customization, feature flags, and contact info.
- Branding uploads (`companyMedia.logo`, `companyMedia.mobile`, `companyMedia.desktop`) are saved as image objects with multiple variants and a separate WebP set.
- `customization.*` is a flat bag of company-wide UX toggles that individual area / channel / feature editors read as defaults. The known keys come from a fixed enum (see `customization.<flag>` below).
- `features.*` is the company-level feature flag map (server-driven). Most pages render or hide blocks depending on the corresponding flag plus a parallel client-feature check.
- `chainSync.ordering`, `placeType: 'CHAIN_*'`, and `customization.restrictChangeFeatureState.*` materially change UX in chain configurations.
- Several payment-related and area-related features depend on `onlinePayment` being true.

```jsonc
{
  "activatedAt": {
    "type": "string | null",
    "format": "ISO datetime",
    "behavior": "Server-managed.",
    "meaning": "Company activation timestamp."
  },
  "active": {
    "type": "boolean",
    "behavior": "Server-managed top-level status.",
    "meaning": "Global company active state."
  },
  "alcoLegalInfo": {
    "type": "object | null",
    "behavior": "Alcohol-related legal info; structure mirrors the `LegalInfo` shape used elsewhere.",
    "shape": {
      "name": { "type": "string", "meaning": "Legal entity name." },
      "localName": { "type": "string", "meaning": "Local-language entity name." },
      "address": {
        "type": "object",
        "shape": {
          "line": { "type": "string" },
          "city": { "type": "string" },
          "country": { "type": "string" },
          "postCode": { "type": "string" },
          "countryCode": { "type": "string" }
        }
      },
      "actualAddress": {
        "type": "object",
        "shape": {
          "line": { "type": "string" }
        }
      },
      "phone": { "type": "string" },
      "email": { "type": "string" },
      "id": { "type": "string", "meaning": "Legal/registration identifier." },
      "vatID": { "type": "string", "meaning": "VAT identifier." },
      "representBy": { "type": "string", "meaning": "Representative." },
      "comment": { "type": "string" },
      "useChoiceQRBranch": {
        "type": "string | null",
        "values": ["PL", null],
        "meaning": "Country-specific branch flag (currently only `PL`)."
      }
    },
    "meaning": "Alcohol-related legal information and compliance settings."
  },
  "blocked": {
    "type": "boolean",
    "behavior": "Server-managed.",
    "meaning": "Global blocked state. If true, the company is restricted by the system or admin."
  },
  "companyMedia": {
    "type": "object",
    "behavior": "Branding and storefront media assets. Each image is a media object with an `originalFile`, an `options` crop config, and a `formats` map of generated variants (and parallel `webp` variants).",
    "shape": {
      "logo": {
        "type": "object | null",
        "meaning": "Main company logo asset.",
        "shape": {
          "_id": { "type": "string", "meaning": "Internal media document identifier." },
          "originalName": { "type": "string", "meaning": "Original uploaded file name." },
          "name": { "type": "string", "meaning": "Internal processed asset name." },
          "filename": { "type": "string", "meaning": "Stored output file name." },
          "size": { "type": "number", "meaning": "File size of the prepared asset." },
          "ext": { "type": "string", "meaning": "File extension such as `png`, `jpeg`, `webp`." },
          "mime": { "type": "string", "meaning": "MIME type such as `image/png`, `image/jpeg`, `image/webp`." },
          "width": { "type": "number", "meaning": "Prepared asset width in pixels." },
          "height": { "type": "number", "meaning": "Prepared asset height in pixels." },
          "url": { "type": "string", "meaning": "Public or CDN URL of the prepared asset." },
          "type": { "type": "string", "meaning": "Media usage type, for example `logo`." },
          "originalFile": {
            "type": "object",
            "meaning": "Original uploaded source file.",
            "shape": {
              "originalName": { "type": "string" },
              "name": { "type": "string" },
              "filename": { "type": "string" },
              "size": { "type": "number" },
              "ext": { "type": "string" },
              "mime": { "type": "string" },
              "width": { "type": "number" },
              "height": { "type": "number" },
              "url": { "type": "string" }
            }
          },
          "options": {
            "type": "object",
            "meaning": "Crop and positioning settings used to prepare the media.",
            "shape": {
              "zoom": { "type": "number" },
              "left": { "type": "number" },
              "top": { "type": "number" },
              "areaWidth": { "type": "number" },
              "areaHeight": { "type": "number" }
            }
          },
          "fetchedUrl": { "type": "string", "meaning": "Remote source URL used for import, if any." },
          "formats": {
            "type": "object",
            "meaning": "Generated asset variants by size or delivery format.",
            "shape": {
              "<variant>": {
                "type": "object",
                "meaning": "Prepared asset variant such as `full`, `thumbnail`, `x256`, `x192`, `x180`, `x152`, `x144`, `x72`, `x48`, `x32`.",
                "shape": {
                  "originalName": { "type": "string" },
                  "name": { "type": "string" },
                  "filename": { "type": "string" },
                  "size": { "type": "number" },
                  "ext": { "type": "string" },
                  "mime": { "type": "string" },
                  "width": { "type": "number" },
                  "height": { "type": "number" },
                  "url": { "type": "string" }
                }
              },
              "webp": {
                "type": "object",
                "meaning": "WebP versions of generated variants.",
                "shape": {
                  "<variant>": "Same media-variant structure as above, but stored as WebP."
                }
              }
            }
          },
          "dbName": { "type": "string", "meaning": "Tenant identifier linked to this media record." },
          "createdAt": { "type": "string", "format": "ISO datetime" },
          "updatedAt": { "type": "string", "format": "ISO datetime" }
        }
      },
      "mobile": {
        "type": "object | null",
        "meaning": "Mobile-specific storefront/banner image. Same structure as `logo` (different `type` value)."
      },
      "desktop": {
        "type": "object | null",
        "meaning": "Desktop storefront/banner image.",
        "shape": {
          "_id": "Same as `logo._id`.",
          "originalName": "Same as `logo.originalName`.",
          "name": "Same as `logo.name`.",
          "filename": "Same as `logo.filename`.",
          "size": "Same as `logo.size`.",
          "ext": "Same as `logo.ext`.",
          "mime": "Same as `logo.mime`.",
          "width": "Same as `logo.width`.",
          "height": "Same as `logo.height`.",
          "type": {
            "type": "string",
            "values": ["template-slider-desktop", "..."],
            "meaning": "Media usage type."
          },
          "url": "Same as `logo.url`.",
          "originalFile": "Same as `logo.originalFile`.",
          "formats": {
            "type": "object",
            "meaning": "Generated desktop image variants such as `thumbnail`, `fullScreen`, `manufactureFullScreen`, `manufacture`, and parallel `webp` variants.",
            "shape": {
              "<variant>": "Same media-variant structure as in `logo.formats`.",
              "webp": {
                "type": "object",
                "shape": {
                  "<variant>": "Same media-variant structure as above, but stored as WebP."
                }
              }
            }
          },
          "dbName": "Same as `logo.dbName`.",
          "createdAt": "Same as `logo.createdAt`.",
          "updatedAt": "Same as `logo.updatedAt`."
        }
      }
    }
  },
  "contactInfo": {
    "type": "object",
    "behavior": "Main contact info shown on the storefront and used for outbound platform communications.",
    "shape": {
      "phone": {
        "type": "string",
        "meaning": "Primary company phone number, usually in international format."
      },
      "address": {
        "type": "object",
        "behavior": "Edited via the address autocomplete; some fields are populated by the provider response and some are user-overridden.",
        "shape": {
          "placeId": { "type": "string", "meaning": "External map provider place identifier." },
          "place": { "type": "string", "meaning": "Full place text returned by the provider." },
          "city": { "type": "string", "meaning": "City." },
          "streetNumber": { "type": "string", "meaning": "Building number." },
          "streetName": { "type": "string", "meaning": "Street name." },
          "sublocality": { "type": "string", "meaning": "District / neighborhood." },
          "sublocalityLevel1": { "type": "string", "meaning": "Normalized district." },
          "state": { "type": "string", "meaning": "State / region." },
          "country": { "type": "string", "meaning": "Country name." },
          "countryCode": { "type": "string", "meaning": "Country code (ISO 3166-1 alpha-2), e.g. `CZ`, `US`, `DE`." },
          "postalCode": { "type": "string", "meaning": "Postal code." },
          "location": {
            "type": "object",
            "shape": {
              "coordinates": {
                "type": "number[]",
                "meaning": "Coordinate pair `[longitude, latitude]`."
              },
              "type": {
                "type": "string",
                "values": ["Point"],
                "meaning": "Geospatial object type."
              }
            }
          },
          "missingData": {
            "type": "object",
            "meaning": "Validation flags showing which address parts are incomplete.",
            "shape": {
              "prediction": { "type": "boolean" },
              "streetNumber": { "type": "boolean" }
            }
          },
          "prediction": { "type": "string", "meaning": "Address prediction text returned by autocomplete." },
          "originalPrediction": {
            "type": "string | null",
            "meaning": "Raw provider prediction before cleanup or confirmation."
          },
          "customPrediction": {
            "type": "string | null",
            "meaning": "Manually edited address prediction overriding the provider value."
          },
          "useCustomMap": {
            "type": "boolean",
            "meaning": "Whether a custom map location/marker is used."
          },
          "userDefinedData": {
            "type": "object | null",
            "meaning": "Manually entered address-related data.",
            "shape": {
              "city": { "type": "string", "meaning": "User-entered city override." }
            }
          }
        }
      },
      "website": {
        "type": "string",
        "meaning": "Company website URL. Can be empty if not set."
      },
      "phoneLabel": {
        "type": "object",
        "meaning": "Localized label for the phone number.",
        "shape": {
          "i18n": {
            "type": "object",
            "meaning": "Localization map by language code.",
            "shape": {
              "<lang>": {
                "type": "object",
                "shape": {
                  "name": {
                    "type": "string",
                    "meaning": "Localized phone label such as `Orders`, `Support`, `Reservations`."
                  }
                }
              }
            }
          }
        }
      },
      "additionalPhones": {
        "type": "array",
        "meaning": "Additional phone numbers.",
        "itemShape": {
          "phone": { "type": "string" },
          "i18n": {
            "type": "object",
            "meaning": "Localization map by language code.",
            "shape": {
              "<lang>": {
                "type": "object",
                "shape": {
                  "name": { "type": "string", "meaning": "Localized phone label." }
                }
              }
            }
          }
        }
      }
    }
  },
  "createdAt": {
    "type": "string",
    "format": "ISO datetime",
    "behavior": "Server-managed.",
    "meaning": "Company object creation timestamp."
  },
  "currency": {
    "type": "string",
    "behavior": "Used everywhere as the company default for prices, tips, deposits, and area-level fees. Some areas overwrite their own currency from this on load.",
    "meaning": "Company currency code (ISO 4217), e.g. `USD`, `EUR`, `CZK`."
  },
  "customization": {
    "type": "object",
    "behavior": "Flat bag of company-wide UX toggles. Most are simple booleans matching the keys below; some are nested objects (`cardMenuOptionShow`, `restrictChangeFeatureState`, `googleServiceConnect`, `lawRequiredLinks`, `feedbackSocNetworkLinks`). All keys are optional — missing keys mean 'not set' (the consumer applies its own default).",
    "shape": {
      "primaryColor": {
        "type": "string",
        "meaning": "Main brand color, HEX value."
      },
      "fontColor": {
        "type": "string",
        "meaning": "Primary text color on branded elements, HEX value."
      },
      "callWaiter": {
        "type": "boolean",
        "meaning": "Enables/disables waiter-call button on dine-in flows."
      },
      "showTableSelector": {
        "type": "boolean",
        "meaning": "Whether the table selector is shown."
      },
      "showOrderTableProgress": {
        "type": "boolean",
        "meaning": "Whether dine-in/table order progress is shown."
      },
      "showOrderDeliveryProgress": {
        "type": "boolean",
        "meaning": "Whether delivery order progress is shown."
      },
      "showOrderTakeawayProgress": {
        "type": "boolean",
        "behavior": "Edited via the takeaway settings page (toggle 'order_progress').",
        "meaning": "Whether takeaway order progress is shown."
      },
      "showDigitalMenuSelector": {
        "type": "boolean",
        "meaning": "Whether the digital menu selector is shown."
      },
      "digitalMenuWishList": {
        "type": "boolean",
        "meaning": "Wishlist functionality for digital menu items."
      },
      "autoCancellationOrder": {
        "type": "number",
        "default": 60,
        "unit": "minutes",
        "behavior": "Default delay before unconfirmed orders are auto-cancelled. Default constant is 60 minutes when seeded by the UI.",
        "meaning": "Auto-cancellation delay."
      },
      "adultAlkoBanner": {
        "type": "object | null",
        "behavior": "Alcohol age restriction banner. UI defaults: age 18, range 1..50.",
        "meaning": "Alcohol age-gate banner configuration."
      },
      "allergenList": {
        "type": "boolean",
        "meaning": "Whether allergen list is shown on menu items."
      },
      "feedbackMandatoryPhone": {
        "type": "boolean",
        "meaning": "Makes phone number required in the feedback flow."
      },
      "feedbackSocNetworkLinks": {
        "type": "array",
        "itemShape": "string  // tuple ['google'?, 'tripAdvisor'?]",
        "meaning": "Which social networks to show as feedback redirect targets."
      },
      "feedbackTableOrderDisabled": {
        "type": "boolean",
        "meaning": "Disables feedback for table orders."
      },
      "requiredEmailOrder": {
        "type": "boolean",
        "meaning": "Makes email required during order placement."
      },
      "requiredEmailBooking": {
        "type": "boolean",
        "meaning": "Makes email required during booking. Mirrors `bookingParams.requiredEmailBooking`."
      },
      "areaSwitcher": {
        "type": "boolean",
        "meaning": "Enables the area-type selector on the storefront."
      },
      "promocodes": {
        "type": "boolean",
        "meaning": "Enables promocodes UX on the storefront."
      },
      "canPreOrder": {
        "type": "boolean",
        "meaning": "Whether pre-order time selection is enabled."
      },
      "showChangePreOrderTime": {
        "type": "boolean",
        "meaning": "Allows customer to change preorder time after placement."
      },
      "hideMinorUnits": {
        "type": "boolean",
        "meaning": "Hides minor currency units (e.g. cents)."
      },
      "hideRequestCustomerVAT": {
        "type": "boolean",
        "meaning": "Hides the customer-VAT request UI."
      },
      "cardMenuOptionShow": {
        "type": "object",
        "meaning": "Per-channel visibility flags for menu card modes.",
        "shape": {
          "readOnly": { "type": "boolean", "meaning": "Show read-only mode card." },
          "simple": { "type": "boolean", "meaning": "Show simple mode card." },
          "delivery": { "type": "boolean", "meaning": "Show delivery mode card." },
          "takeaway": { "type": "boolean", "meaning": "Show takeaway mode card." }
        }
      },
      "tablePaymentDisableSplit": {
        "type": "boolean",
        "behavior": "Acts as default for area-level `config.tablePaymentDisableSplit` on table-payment areas.",
        "meaning": "Disables split-bill for QR/table payments venue-wide."
      },
      "menuFavorites": {
        "type": "boolean",
        "meaning": "Enables favorite dishes functionality."
      },
      "menuFavoritesCounter": {
        "type": "boolean",
        "meaning": "Shows favorites counter in the interface."
      },
      "menuFavoriteDishSlider": {
        "type": "boolean",
        "meaning": "Enables highlighted favorite dish slider."
      },
      "shareItemUrl": {
        "type": "boolean",
        "meaning": "Enables share-item URL functionality."
      },
      "clientMenuTileStyle": {
        "type": "boolean",
        "meaning": "Enables tile-style menu presentation."
      },
      "requiredPhoneTableOrder": {
        "type": "boolean",
        "meaning": "Makes phone required for table orders."
      },
      "chainMenuSync": {
        "type": "boolean",
        "meaning": "Enables menu sync across chain locations."
      },
      "platformPages": {
        "type": "object",
        "meaning": "Platform-level pages visibility configuration.",
        "shape": {
          "termsOfUse": {
            "type": "boolean",
            "meaning": "Whether the Terms of Use page is exposed."
          }
        }
      },
      "QRPaymentsNoLimit": {
        "type": "boolean",
        "behavior": "Acts as default for area-level `config.QRPaymentsNoLimit` on table-payment areas (24/7 service).",
        "meaning": "Whether QR/table payments operate 24/7 by default."
      },
      "tableOrderNoLimit": {
        "type": "boolean",
        "meaning": "Enables unlimited table-order mode (no working-hours gate)."
      },
      "multipleMarketplacePartner": {
        "type": "boolean",
        "meaning": "Enables multi-place / multi-partner marketplace mode."
      },
      "chainCityPicker": {
        "type": "boolean",
        "meaning": "Enables the chain-city picker on the storefront."
      },
      "requiredNameTableOrder": {
        "type": "boolean",
        "meaning": "Makes customer name required for table orders."
      },
      "requiredEmailTableOrder": {
        "type": "boolean",
        "meaning": "Makes email required for table orders."
      },
      "expandPaymentMethodsSelector": {
        "type": "boolean",
        "meaning": "Expands/emphasises the payment methods selector."
      },
      "requiredPhoneQRPaymentOrder": {
        "type": "boolean",
        "meaning": "Makes phone required in QR payment flow."
      },
      "requiredNameQRPaymentOrder": {
        "type": "boolean",
        "meaning": "Makes customer name required in QR payment flow."
      },
      "requiredEmailQRPaymentOrder": {
        "type": "boolean",
        "meaning": "Makes email required in QR payment flow."
      },
      "takeawayDeliveryMenuSync": {
        "type": "boolean",
        "meaning": "Whether takeaway and delivery menus are synchronised."
      },
      "menuHideUnavailableCounters": {
        "type": "boolean",
        "meaning": "Hide unavailable counters on the menu."
      },
      "allowAddManualBonuses": {
        "type": "boolean",
        "meaning": "Whether staff can manually add loyalty bonuses."
      },
      "staffAllowAdyenConnect": {
        "type": "boolean",
        "meaning": "Whether staff are allowed to connect Adyen accounts."
      },
      "lawRequiredLinks": {
        "type": "object",
        "behavior": "Country/legal-required link slots, populated only when relevant.",
        "shape": {
          "smiley": {
            "type": "object | null",
            "shape": {
              "url": { "type": "string", "meaning": "Smiley scheme URL (food-safety rating)." }
            }
          }
        }
      },
      "googleServiceConnect": {
        "type": "object",
        "shape": {
          "connectionStatus": {
            "type": "string",
            "values": ["IDLE", "IN_PROGRESS", "MODERATION", "DONE", "ACCEPTED", "DECLINED", "FAILED"],
            "meaning": "Google integration connection state (mirrors `GoogleConnectStatus`)."
          },
          "reviewRedirectUrl": {
            "type": "string | null",
            "meaning": "Redirect URL for Google reviews redirect flow."
          }
        }
      },
      "restrictChangeFeatureState": {
        "type": "object",
        "behavior": "When set, the corresponding feature toggle is hidden from non chain-owner users on the feature page (used to enforce platform-level decisions over a venue's per-feature controls).",
        "shape": {
          "canOrderDelivery": { "type": "boolean" },
          "canOrderTakeaway": { "type": "boolean" }
        }
      }
    }
  },
  "defaultLanguage": {
    "type": "string",
    "behavior": "ISO 639 language code; used as fallback when a localized field has no entry for the current language. Allowed values come from the `CountryLangEnum` set used elsewhere in the app: `uk`, `cz`, `en`, `da`, `de`, `es`, `fi`, `fr`, `it`, `nl`, `no`, `pl`, `ru`, `sv`, `sk`, `tr`, `hu`, `zh`, `kk`, `et`, `lt`, `lv`, `ge`, `ro`.",
    "meaning": "Default storefront language code such as `en`, `uk`, `cs`."
  },
  "description": {
    "type": "string",
    "meaning": "Short company description."
  },
  "domain": {
    "type": "string",
    "behavior": "Server-managed.",
    "meaning": "Platform subdomain or public slug."
  },
  "email": {
    "type": "string",
    "meaning": "Main company email. Can be empty if not set."
  },
  "expired": {
    "type": "boolean",
    "behavior": "Server-derived flag.",
    "meaning": "Subscription or access expiration flag."
  },
  "features": {
    "type": "object",
    "behavior": "Company-level feature flags. Each key is a boolean. Most pages also check a parallel client-feature (`useClientFeature(...)`); when both are needed, the page is gated by both. Keys mirror the `FeaturesType` enum.",
    "shape": {
      "<featureFlag>": { "type": "boolean" },
      "appAccess": { "type": "boolean", "meaning": "App-based access." },
      "areaSwitcher": { "type": "boolean", "meaning": "Service area switching on storefront." },
      "loyalty": { "type": "boolean", "meaning": "Loyalty program functionality." },
      "loyaltyGift": { "type": "boolean", "meaning": "Gift-related loyalty functionality." },
      "booking": { "type": "boolean", "meaning": "Booking / reservation feature." },
      "brandPartners": { "type": "boolean", "meaning": "Partner-brand functionality." },
      "canOrderDelivery": { "type": "boolean", "meaning": "Delivery ordering channel." },
      "canOrderTable": { "type": "boolean", "meaning": "Table ordering channel." },
      "canOrderTakeaway": { "type": "boolean", "meaning": "Takeaway ordering channel." },
      "chainMenuSync": { "type": "boolean", "meaning": "Menu sync across chain locations." },
      "chainOneMenu": { "type": "boolean", "meaning": "Chain-wide single-menu mode." },
      "chainOrdering": { "type": "boolean", "meaning": "Chain-wide ordering mode." },
      "clientCRM": { "type": "boolean", "meaning": "Customer CRM functionality." },
      "clientFeedbackReply": { "type": "boolean", "meaning": "Replying to client feedback." },
      "clientMailing": { "type": "boolean", "meaning": "Customer mailing / campaign tools." },
      "couriers": { "type": "boolean", "meaning": "Courier-related functionality." },
      "customDomain": { "type": "boolean", "meaning": "Custom domain support." },
      "customerTips": { "type": "boolean", "meaning": "Customer tipping capability." },
      "delivery": { "type": "boolean", "meaning": "Delivery module." },
      "emailCompanyLogo": { "type": "boolean", "meaning": "Company logo usage in emails." },
      "feedbackOrder": { "type": "boolean", "meaning": "Order feedback collection." },
      "feedbackRestaurant": { "type": "boolean", "meaning": "Restaurant feedback collection." },
      "fiscal": { "type": "boolean", "meaning": "Fiscal printing." },
      "geolocation": { "type": "boolean", "meaning": "Geolocation-based functionality." },
      "googleConnect": { "type": "boolean", "meaning": "General Google integration." },
      "googleConnectBooking": { "type": "boolean", "meaning": "Google booking integration." },
      "googleConnectReviews": { "type": "boolean", "meaning": "Google reviews integration." },
      "marketing": { "type": "boolean", "meaning": "Marketing module." },
      "marketplace": { "type": "boolean", "meaning": "Marketplace integration features." },
      "messageSender": { "type": "boolean", "meaning": "Customer message sender (alias for `clientMailing`)." },
      "multiPosID": { "type": "boolean", "meaning": "Multiple POS ids per area." },
      "notifyControlCenter": { "type": "boolean", "meaning": "Notification control center module." },
      "onePlusOne": { "type": "boolean", "meaning": "1+1 promotion module." },
      "pos": { "type": "boolean", "meaning": "POS module." },
      "posSettings": { "type": "boolean", "meaning": "POS settings (second-pass settings form)." },
      "posPaymentTable": { "type": "boolean", "meaning": "POS / QR table payment module." },
      "promocodes": { "type": "boolean", "meaning": "Promocode support." },
      "pwa": { "type": "boolean", "meaning": "Progressive Web App capability." },
      "recommendations": { "type": "boolean", "meaning": "Recommendations functionality." },
      "seo": { "type": "boolean", "meaning": "SEO-related functionality." },
      "simplePDFMenu": { "type": "boolean", "meaning": "Simple PDF menu support." },
      "smsDeliveryTakeaway": { "type": "boolean", "meaning": "SMS notifications for delivery/takeaway flows." },
      "staff": { "type": "boolean", "meaning": "Staff management." },
      "telegram": { "type": "boolean", "meaning": "Telegram integration." },
      "translations": { "type": "boolean", "meaning": "Multilingual / translations capability." },
      "whiteLabel": { "type": "boolean", "meaning": "White-label mode." },
      "whiteLabelTitle": { "type": "boolean", "meaning": "Custom white-label title behavior." }
    }
  },
  "maintenance": {
    "type": "boolean",
    "behavior": "When true, the platform shows a maintenance state.",
    "meaning": "Maintenance mode flag."
  },
  "name": {
    "type": "string",
    "meaning": "Company or venue display name."
  },
  "onlinePayment": {
    "type": "boolean",
    "behavior": "Acts as a master gate for online payment options. The `online` payment method on areas (`paymentMethods.online`) and the booking-deposit block are only visible when this is true.",
    "meaning": "Whether online payment is enabled for the venue."
  },
  "placeType": {
    "type": "string",
    "values": ["SIMPLE", "CHAIN_MAIN", "CHAIN_BRANCH"],
    "default": "SIMPLE",
    "behavior": "Discriminates single-tenant venues from chain main / chain branch tenants. Many UI affordances (chain owner controls, chain promocodes editor, chain ordering sync) are only visible to `CHAIN_MAIN`.",
    "meaning": "Internal company / place mode."
  },
  "planDescription": {
    "type": "string",
    "behavior": "Server-managed.",
    "meaning": "Human-readable tariff or plan description."
  },
  "ready": {
    "type": "boolean",
    "behavior": "Server-managed.",
    "meaning": "Readiness flag showing whether required setup is complete."
  },
  "socialNetworks": {
    "type": "object",
    "behavior": "Optional social links. Known keys are listed below; missing keys mean 'not set'.",
    "shape": {
      "fb": { "type": "string", "meaning": "Facebook URL." },
      "google": { "type": "string", "meaning": "Google URL." },
      "instagram": { "type": "string", "meaning": "Instagram URL." },
      "ok": { "type": "string", "meaning": "OK.ru URL." },
      "telegram": { "type": "string", "meaning": "Telegram URL." },
      "tiktok": { "type": "string", "meaning": "TikTok URL." },
      "tripAdvisor": { "type": "string", "meaning": "TripAdvisor URL." },
      "vk": { "type": "string", "meaning": "VK URL." }
    }
  },
  "timezone": {
    "type": "string",
    "behavior": "Used everywhere as the canonical tenant timezone.",
    "meaning": "IANA timezone string such as `Europe/Prague`."
  },
  "type": {
    "type": "string",
    "values": ["restaurant", "..."],
    "meaning": "Business type."
  },
  "updatedAt": {
    "type": "string",
    "format": "ISO datetime",
    "behavior": "Server-managed.",
    "meaning": "Company object update timestamp."
  },
  "wifi": {
    "type": "string",
    "meaning": "Wi-Fi information for guests. Can be empty if not used."
  },
  "workTime": {
    "type": "array",
    "behavior": "Default working schedule for the company. Used as a fallback when an area's own working hours are not configured (e.g. when first enabling discount limited-time mode in delivery/takeaway forms).",
    "meaning": "Working hours array (one entry per day of week).",
    "itemShape": {
      "dayOfWeek": {
        "type": "number",
        "values": [0, 1, 2, 3, 4, 5, 6],
        "meaning": "Day index 0..6."
      },
      "active": {
        "type": "boolean",
        "meaning": "Whether the company is open on that day."
      },
      "from": {
        "type": "string",
        "format": "HH:mm",
        "meaning": "Opening time."
      },
      "till": {
        "type": "string",
        "format": "HH:mm",
        "meaning": "Closing time."
      }
    }
  },
  "b2cHide": {
    "type": "boolean",
    "meaning": "Public B2C visibility flag. If true, the company is hidden from public storefront discovery."
  },
  "accountantInfo": {
    "type": "object",
    "shape": {
      "emails": {
        "type": "array",
        "meaning": "Accounting-related email entries.",
        "itemShape": {
          "email": { "type": "string", "meaning": "Accounting-related email address." }
        }
      }
    }
  },
  "customerCustomType": {
    "type": "string",
    "values": ["Pizza", "Cafe", "Bar", "..."],
    "meaning": "Custom business label."
  },
  "dbName": {
    "type": "string",
    "behavior": "Server-managed tenant identifier; used to address the tenant across APIs.",
    "meaning": "Technical tenant or database identifier."
  },
  "areaOrderAcceptance": {
    "type": "object",
    "behavior": "Temporary order-acceptance pause state per channel. Used by the takeaway / delivery feature labels (`isServicePaused`) to render a 'paused' indicator in the UI.",
    "shape": {
      "takeaway": {
        "type": "object",
        "meaning": "Takeaway order acceptance state.",
        "shape": {
          "paused": { "type": "boolean" },
          "resumeAt": {
            "type": "string | null",
            "format": "ISO datetime",
            "meaning": "Scheduled resume timestamp."
          }
        }
      },
      "delivery": {
        "type": "object",
        "meaning": "Delivery order acceptance state.",
        "shape": {
          "paused": { "type": "boolean" },
          "resumeAt": {
            "type": "string | null",
            "format": "ISO datetime",
            "meaning": "Scheduled resume timestamp."
          }
        }
      }
    }
  },
  "platform": {
    "type": "object",
    "behavior": "Platform-managed company settings (server-side configured).",
    "shape": {
      "marketingBanners": {
        "type": "object",
        "behavior": "Map of banner id → banner entry.",
        "shape": {
          "<bannerId>": {
            "type": "object",
            "shape": {
              "createdAt": { "type": "string", "format": "ISO datetime" },
              "priority": { "type": "number" },
              "media": {
                "type": "object",
                "behavior": "Per-language media payload.",
                "shape": {
                  "<lang>": {
                    "type": "object",
                    "shape": {
                      "actionUrl": { "type": "string" },
                      "imageUrl": { "type": "string" }
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
  },
  "customerDomain": {
    "type": "string | null",
    "behavior": "Used as the public link host for booking / takeaway / delivery URLs (`https://${customerDomain || window.location.host}/<channel>`).",
    "meaning": "Custom external domain assigned to the company."
  },
  "customDomain": {
    "type": "string",
    "behavior": "Internal alias for the custom domain. Used by the area page builders to compose customer-facing URLs.",
    "meaning": "Custom external domain assigned to the company."
  },
  "chainSync": {
    "type": "object",
    "behavior": "Chain-level sync flags. When the relevant flag is true, the corresponding feature is regulated at the chain level — the venue's editor renders an info banner and a 'Go to Chain Backoffice' link, and the local form is read-only or partially hidden.",
    "shape": {
      "menu": { "type": "boolean", "meaning": "Menu is sync'd across chain branches." },
      "promocodes": { "type": "boolean", "meaning": "Promocodes are sync'd across chain branches." },
      "loyalty": { "type": "boolean", "meaning": "Loyalty is sync'd across chain branches." },
      "loyaltyGift": { "type": "boolean", "meaning": "Loyalty gifts are sync'd across chain branches." },
      "ordering": {
        "type": "boolean",
        "behavior": "When true, takeaway/delivery editors render the chain-sync banner instead of the per-venue form, and the public-link block is hidden.",
        "meaning": "Ordering is sync'd across chain branches."
      }
    }
  }
}
```
