# Company data

JSON-shaped reference for the `Company data` object.

Notes:
- This file intentionally mirrors the API response structure.
- It uses `jsonc` style so descriptions can live next to fields.
- Repeated media objects follow the same internal pattern unless stated otherwise.

```jsonc
{
  "activatedAt": {
    "type": "string | null",
    "format": "ISO datetime",
    "meaning": "Company activation timestamp."
  },
  "active": {
    "type": "boolean",
    "meaning": "Global company active state."
  },
  "alcoLegalInfo": {
    "type": "object | null",
    "meaning": "Alcohol-related legal information and compliance settings, if used."
  },
  "blocked": {
    "type": "boolean",
    "meaning": "Global blocked state. If true, the company is likely restricted by the system or admin."
  },
  "companyMedia": {
    "type": "object",
    "meaning": "Branding and storefront media assets.",
    "shape": {
      "logo": {
        "type": "object | null",
        "meaning": "Main company logo asset.",
        "shape": {
          "_id": {
            "type": "string",
            "meaning": "Internal media document identifier."
          },
          "originalName": {
            "type": "string",
            "meaning": "Original uploaded file name."
          },
          "name": {
            "type": "string",
            "meaning": "Internal processed asset name."
          },
          "filename": {
            "type": "string",
            "meaning": "Stored output file name."
          },
          "size": {
            "type": "number",
            "meaning": "File size of the prepared asset."
          },
          "ext": {
            "type": "string",
            "meaning": "File extension such as `png`, `jpeg`, `webp`."
          },
          "mime": {
            "type": "string",
            "meaning": "MIME type such as `image/png`, `image/jpeg`, `image/webp`."
          },
          "width": {
            "type": "number",
            "meaning": "Prepared asset width in pixels."
          },
          "height": {
            "type": "number",
            "meaning": "Prepared asset height in pixels."
          },
          "url": {
            "type": "string",
            "meaning": "Public or CDN URL of the prepared asset."
          },
          "type": {
            "type": "string",
            "meaning": "Media usage type, for example `logo`."
          },
          "originalFile": {
            "type": "object",
            "meaning": "Original uploaded source file.",
            "shape": {
              "originalName": {
                "type": "string",
                "meaning": "Original uploaded file name."
              },
              "name": {
                "type": "string",
                "meaning": "Internal source asset name."
              },
              "filename": {
                "type": "string",
                "meaning": "Stored original file name."
              },
              "size": {
                "type": "number",
                "meaning": "Original file size."
              },
              "ext": {
                "type": "string",
                "meaning": "Original file extension."
              },
              "mime": {
                "type": "string",
                "meaning": "Original MIME type."
              },
              "width": {
                "type": "number",
                "meaning": "Original image width."
              },
              "height": {
                "type": "number",
                "meaning": "Original image height."
              },
              "url": {
                "type": "string",
                "meaning": "Public or CDN URL of the original uploaded file."
              }
            }
          },
          "options": {
            "type": "object",
            "meaning": "Crop and positioning settings used to prepare the media.",
            "shape": {
              "zoom": {
                "type": "number",
                "meaning": "Crop zoom factor."
              },
              "left": {
                "type": "number",
                "meaning": "Horizontal crop offset."
              },
              "top": {
                "type": "number",
                "meaning": "Vertical crop offset."
              },
              "areaWidth": {
                "type": "number",
                "meaning": "Crop area width."
              },
              "areaHeight": {
                "type": "number",
                "meaning": "Crop area height."
              }
            }
          },
          "fetchedUrl": {
            "type": "string",
            "meaning": "Remote source URL used for import, if any. Can be empty."
          },
          "formats": {
            "type": "object",
            "meaning": "Generated asset variants by size or delivery format.",
            "shape": {
              "<variant>": {
                "type": "object",
                "meaning": "Prepared asset variant such as `full`, `thumbnail`, `x256`, `x192`, `x180`, `x152`, `x144`, `x72`, `x48`, `x32`.",
                "shape": {
                  "originalName": {
                    "type": "string",
                    "meaning": "Original or generated source name for this variant."
                  },
                  "name": {
                    "type": "string",
                    "meaning": "Internal asset name for this variant."
                  },
                  "filename": {
                    "type": "string",
                    "meaning": "Stored file name for this variant."
                  },
                  "size": {
                    "type": "number",
                    "meaning": "Variant file size."
                  },
                  "ext": {
                    "type": "string",
                    "meaning": "Variant extension."
                  },
                  "mime": {
                    "type": "string",
                    "meaning": "Variant MIME type."
                  },
                  "width": {
                    "type": "number",
                    "meaning": "Variant width."
                  },
                  "height": {
                    "type": "number",
                    "meaning": "Variant height."
                  },
                  "url": {
                    "type": "string",
                    "meaning": "Public or CDN URL of this variant."
                  }
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
          "dbName": {
            "type": "string",
            "meaning": "Tenant or database identifier linked to this media record."
          },
          "createdAt": {
            "type": "string",
            "format": "ISO datetime",
            "meaning": "Media object creation timestamp."
          },
          "updatedAt": {
            "type": "string",
            "format": "ISO datetime",
            "meaning": "Media object update timestamp."
          }
        }
      },
      "mobile": {
        "type": "object | null",
        "meaning": "Mobile-specific storefront/banner image. If present, follows the same general media-asset pattern as desktop."
      },
      "desktop": {
        "type": "object | null",
        "meaning": "Desktop storefront/banner image.",
        "shape": {
          "_id": "Same meaning and type pattern as `companyMedia.logo._id`.",
          "originalName": "Same meaning and type pattern as `companyMedia.logo.originalName`.",
          "name": "Same meaning and type pattern as `companyMedia.logo.name`.",
          "filename": "Same meaning and type pattern as `companyMedia.logo.filename`.",
          "size": "Same meaning and type pattern as `companyMedia.logo.size`.",
          "ext": "Same meaning and type pattern as `companyMedia.logo.ext`.",
          "mime": "Same meaning and type pattern as `companyMedia.logo.mime`.",
          "width": "Same meaning and type pattern as `companyMedia.logo.width`.",
          "height": "Same meaning and type pattern as `companyMedia.logo.height`.",
          "type": {
            "type": "string",
            "meaning": "Media usage type, for example `template-slider-desktop`."
          },
          "url": "Same meaning and type pattern as `companyMedia.logo.url`.",
          "originalFile": "Same meaning and type pattern as `companyMedia.logo.originalFile`.",
          "formats": {
            "type": "object",
            "meaning": "Generated desktop image variants such as `thumbnail`, `fullScreen`, `manufactureFullScreen`, `manufacture`, and `webp`.",
            "shape": {
              "<variant>": "Same media-variant structure as in `companyMedia.logo.formats`.",
              "webp": {
                "type": "object",
                "meaning": "WebP versions of desktop variants.",
                "shape": {
                  "<variant>": "Same media-variant structure as above, but stored as WebP."
                }
              }
            }
          },
          "dbName": "Same meaning and type pattern as `companyMedia.logo.dbName`.",
          "createdAt": "Same meaning and type pattern as `companyMedia.logo.createdAt`.",
          "updatedAt": "Same meaning and type pattern as `companyMedia.logo.updatedAt`."
        }
      }
    }
  },
  "contactInfo": {
    "type": "object",
    "meaning": "Main contact information shown or used by the platform.",
    "shape": {
      "phone": {
        "type": "string",
        "meaning": "Primary company phone number, usually in international format."
      },
      "address": {
        "type": "object",
        "meaning": "Structured company address and map-related data.",
        "shape": {
          "placeId": {
            "type": "string",
            "meaning": "External map provider place identifier."
          },
          "streetNumber": {
            "type": "string",
            "meaning": "Building number."
          },
          "streetName": {
            "type": "string",
            "meaning": "Street name."
          },
          "sublocality": {
            "type": "string",
            "meaning": "District, neighborhood, or sublocality."
          },
          "sublocalityLevel1": {
            "type": "string",
            "meaning": "Normalized district or sublocality value."
          },
          "state": {
            "type": "string",
            "meaning": "State, region, or major administrative area."
          },
          "country": {
            "type": "string",
            "meaning": "Country name."
          },
          "countryCode": {
            "type": "string",
            "meaning": "Country code such as `CZ`, `US`, `DE`."
          },
          "postalCode": {
            "type": "string",
            "meaning": "Postal code."
          },
          "location": {
            "type": "object",
            "meaning": "Geospatial coordinates.",
            "shape": {
              "coordinates": {
                "type": "number[]",
                "meaning": "Coordinate pair, likely stored as `[longitude, latitude]`."
              },
              "type": {
                "type": "string",
                "meaning": "Geospatial object type, usually `Point`."
              }
            }
          },
          "missingData": {
            "type": "object",
            "meaning": "Validation flags showing which address parts are incomplete.",
            "shape": {
              "prediction": {
                "type": "boolean",
                "meaning": "Whether provider prediction data is missing."
              },
              "streetNumber": {
                "type": "boolean",
                "meaning": "Whether street number is missing."
              }
            }
          },
          "prediction": {
            "type": "string",
            "meaning": "Address prediction text returned by address autocomplete/provider."
          },
          "originalPrediction": {
            "type": "string",
            "meaning": "Original raw provider prediction before cleanup or confirmation. Can be empty."
          },
          "useCustomMap": {
            "type": "boolean",
            "meaning": "Whether a custom map location or marker is used."
          },
          "userDefinedData": {
            "type": "object | null",
            "meaning": "Additional manually entered address-related data."
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
                "meaning": "Localized phone label entry.",
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
        "meaning": "Additional phone numbers. Can be empty if not configured."
      }
    }
  },
  "createdAt": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "Company object creation timestamp."
  },
  "currency": {
    "type": "string",
    "meaning": "Company currency code such as `USD`, `EUR`, `CZK`."
  },
  "customization": {
    "type": "object",
    "meaning": "Client-facing storefront and order-flow customization settings.",
    "shape": {
      "primaryColor": {
        "type": "string",
        "meaning": "Main brand color, usually a HEX value."
      },
      "showTableSelector": {
        "type": "boolean",
        "meaning": "Controls whether the table selector is shown."
      },
      "fontColor": {
        "type": "string",
        "meaning": "Primary text color on branded elements, usually a HEX value."
      },
      "showOrderTableProgress": {
        "type": "boolean",
        "meaning": "Controls whether dine-in/table order progress is shown."
      },
      "callWaiter": {
        "type": "boolean",
        "meaning": "Enables or disables waiter call functionality."
      },
      "autoCancellationOrder": {
        "type": "number",
        "meaning": "Automatic order cancellation timeout, most likely measured in minutes."
      },
      "showOrderDeliveryProgress": {
        "type": "boolean",
        "meaning": "Controls whether delivery order progress is shown."
      },
      "showDigitalMenuSelector": {
        "type": "boolean",
        "meaning": "Controls whether the digital menu selector is shown."
      },
      "allergenList": {
        "type": "boolean",
        "meaning": "Enables or disables allergen list visibility."
      },
      "customTemplate": {
        "type": "boolean",
        "meaning": "Enables custom storefront template behavior."
      },
      "feedbackMandatoryPhone": {
        "type": "boolean",
        "meaning": "Makes phone number required in the feedback flow."
      },
      "menuFavorites": {
        "type": "boolean",
        "meaning": "Enables favorite dishes functionality."
      },
      "menuFavoritesCounter": {
        "type": "boolean",
        "meaning": "Shows favorites counter in the interface."
      },
      "clientMenuTileStyle": {
        "type": "boolean",
        "meaning": "Enables tile-style menu presentation."
      },
      "cardMenuOptionShow": {
        "type": "object",
        "meaning": "Visibility flags for menu card modes.",
        "shape": {
          "readOnly": {
            "type": "boolean",
            "meaning": "Shows read-only mode."
          },
          "simple": {
            "type": "boolean",
            "meaning": "Shows simple mode."
          },
          "delivery": {
            "type": "boolean",
            "meaning": "Shows delivery mode."
          },
          "takeaway": {
            "type": "boolean",
            "meaning": "Shows takeaway mode."
          }
        }
      },
      "hideMinorUnits": {
        "type": "boolean",
        "meaning": "Hides minor currency units such as cents."
      },
      "showOrderTakeawayProgress": {
        "type": "boolean",
        "meaning": "Controls whether takeaway order progress is shown."
      },
      "menuFavoriteDishSlider": {
        "type": "boolean",
        "meaning": "Enables highlighted favorite dish slider or similar block."
      },
      "requiredPhoneTableOrder": {
        "type": "boolean",
        "meaning": "Makes phone required for table orders."
      },
      "feedbackTableOrderDisabled": {
        "type": "boolean",
        "meaning": "Disables feedback for table orders."
      },
      "platformPages": {
        "type": "object",
        "meaning": "Platform-level pages visibility configuration.",
        "shape": {
          "termsOfUse": {
            "type": "boolean",
            "meaning": "Controls whether Terms of Use page is enabled or exposed."
          }
        }
      },
      "adultAlkoBanner": {
        "type": "object | null",
        "meaning": "Alcohol age restriction banner configuration."
      },
      "showChangePreOrderTime": {
        "type": "boolean",
        "meaning": "Allows customer to change preorder time."
      },
      "requiredNameQRPaymentOrder": {
        "type": "boolean",
        "meaning": "Makes customer name required in QR payment flow."
      },
      "requiredPhoneQRPaymentOrder": {
        "type": "boolean",
        "meaning": "Makes customer phone required in QR payment flow."
      },
      "requiredNameTableOrder": {
        "type": "boolean",
        "meaning": "Makes customer name required for table orders."
      },
      "takeawayDeliveryMenuSync": {
        "type": "boolean",
        "meaning": "Controls whether takeaway and delivery menus are synchronized."
      },
      "requiredEmailOrder": {
        "type": "boolean",
        "meaning": "Makes email required during order placement."
      },
      "expandPaymentMethodsSelector": {
        "type": "boolean",
        "meaning": "Expands or emphasizes the payment methods selector."
      },
      "tableOrderNoLimit": {
        "type": "boolean",
        "meaning": "Enables unlimited table-order mode."
      },
      "googleServiceConnect": {
        "type": "object",
        "meaning": "Google-related integration status and links.",
        "shape": {
          "connectionStatus": {
            "type": "string",
            "meaning": "Integration state, likely enum-like values such as `DECLINED`, `PENDING`, `CONNECTED`."
          },
          "reviewRedirectUrl": {
            "type": "string | null",
            "meaning": "Redirect URL for review or Google-service connection flow."
          }
        }
      }
    }
  },
  "defaultLanguage": {
    "type": "string",
    "meaning": "Default storefront language code such as `en`, `uk`, `cs`."
  },
  "description": {
    "type": "string",
    "meaning": "Short company description."
  },
  "domain": {
    "type": "string",
    "meaning": "Platform subdomain or public slug."
  },
  "email": {
    "type": "string",
    "meaning": "Main company email. Can be empty if not set."
  },
  "expired": {
    "type": "boolean",
    "meaning": "Subscription or access expiration flag."
  },
  "features": {
    "type": "object",
    "meaning": "Company-level feature flags.",
    "shape": {
      "<featureFlag>": {
        "type": "boolean",
        "meaning": "Enables or disables a specific product capability."
      },
      "posPaymentTable": "POS payment in table-related flows.",
      "canOrderTable": "Table ordering capability.",
      "canOrderDelivery": "Delivery ordering capability.",
      "canOrderTakeaway": "Takeaway ordering capability.",
      "booking": "Booking or reservation capability.",
      "translations": "Multilingual/translations capability.",
      "feedbackRestaurant": "Restaurant feedback collection.",
      "smsDeliveryTakeaway": "SMS notifications for delivery and takeaway flows.",
      "simplePDFMenu": "Simple PDF menu support.",
      "pwa": "Progressive Web App capability.",
      "pos": "POS module capability.",
      "whiteLabel": "White-label mode.",
      "delivery": "Delivery module capability.",
      "geolocation": "Geolocation-based functionality.",
      "areaSwitcher": "Service area switching.",
      "emailCompanyLogo": "Company logo usage in emails.",
      "seo": "SEO-related functionality.",
      "recommendations": "Recommendations functionality.",
      "customerTips": "Customer tipping capability.",
      "chainTakeawayDelivery": "Chain-level takeaway and delivery behavior.",
      "chainMenuSync": "Menu sync across chain locations.",
      "brandPartners": "Partner-brand related functionality.",
      "marketplace": "Marketplace-related functionality.",
      "couriers": "Courier-related functionality.",
      "customDomain": "Custom domain support.",
      "clientCRM": "Customer CRM functionality.",
      "clientMailing": "Customer mailing or campaign tools.",
      "promocodes": "Promocode support.",
      "staff": "Staff management functionality.",
      "googleConnectBooking": "Google booking integration.",
      "appAccess": "App-based access.",
      "loyalty": "Loyalty program functionality.",
      "multiPosID": "Multiple POS identifiers or multi-POS support.",
      "whiteLabelTitle": "Custom white-label title behavior.",
      "loyaltyGift": "Gift-related loyalty functionality.",
      "clientFeedbackReply": "Replying to client feedback.",
      "notifyControlCenter": "Notifications to control center or back-office monitoring.",
      "googleConnect": "General Google integration."
    }
  },
  "maintenance": {
    "type": "boolean",
    "meaning": "Maintenance mode flag."
  },
  "name": {
    "type": "string",
    "meaning": "Company or venue display name."
  },
  "onlinePayment": {
    "type": "boolean",
    "meaning": "Enables online payment."
  },
  "placeType": {
    "type": "string",
    "meaning": "Internal company or place mode, often enum-like, for example `SIMPLE`."
  },
  "planDescription": {
    "type": "string",
    "meaning": "Human-readable tariff or plan description."
  },
  "ready": {
    "type": "boolean",
    "meaning": "Readiness flag showing whether required setup is complete."
  },
  "socialNetworks": {
    "type": "object",
    "meaning": "Social links object. Can be empty or contain platform-specific keys and URLs."
  },
  "timezone": {
    "type": "string",
    "meaning": "IANA timezone string such as `Europe/Prague`."
  },
  "type": {
    "type": "string",
    "meaning": "Business type such as `restaurant`."
  },
  "updatedAt": {
    "type": "string",
    "format": "ISO datetime",
    "meaning": "Company object update timestamp."
  },
  "wifi": {
    "type": "string",
    "meaning": "Wi-Fi information for guests. Can be empty if not used."
  },
  "workTime": {
    "type": "array",
    "meaning": "Working hours array, usually one object per day of week.",
    "itemShape": {
      "dayOfWeek": {
        "type": "number",
        "meaning": "Day index, usually `0..6`. The exact day mapping should be confirmed in product logic."
      },
      "active": {
        "type": "boolean",
        "meaning": "Whether the company is open on that day."
      },
      "from": {
        "type": "string",
        "meaning": "Opening time, usually stored as a time string."
      },
      "till": {
        "type": "string",
        "meaning": "Closing time, usually stored as a time string."
      }
    }
  },
  "b2cHide": {
    "type": "boolean",
    "meaning": "Public B2C visibility flag. If true, the company is likely hidden from public storefront discovery."
  },
  "accountantInfo": {
    "type": "object",
    "meaning": "Accounting-related contact data.",
    "shape": {
      "emails": {
        "type": "array",
        "meaning": "List of accounting-related email entries.",
        "itemShape": {
          "email": {
            "type": "string",
            "meaning": "Accounting-related email address."
          }
        }
      }
    }
  },
  "customerCustomType": {
    "type": "string",
    "meaning": "Custom business label such as `Pizza`, `Cafe`, `Bar`."
  },
  "dbName": {
    "type": "string",
    "meaning": "Technical tenant or database identifier."
  },
  "areaOrderAcceptance": {
    "type": "object",
    "meaning": "Temporary order acceptance state by order channel.",
    "shape": {
      "takeaway": {
        "type": "object",
        "meaning": "Takeaway order acceptance state.",
        "shape": {
          "paused": {
            "type": "boolean",
            "meaning": "Whether takeaway order acceptance is paused."
          },
          "resumeAt": {
            "type": "string | null",
            "format": "ISO datetime",
            "meaning": "Scheduled resume timestamp for takeaway acceptance."
          }
        }
      },
      "delivery": {
        "type": "object",
        "meaning": "Delivery order acceptance state.",
        "shape": {
          "paused": {
            "type": "boolean",
            "meaning": "Whether delivery order acceptance is paused."
          },
          "resumeAt": {
            "type": "string | null",
            "format": "ISO datetime",
            "meaning": "Scheduled resume timestamp for delivery acceptance."
          }
        }
      }
    }
  },
  "platform": {
    "type": "object",
    "meaning": "Platform-managed company settings.",
    "shape": {
      "marketingBanners": {
        "type": "object",
        "meaning": "Marketing banners configuration. Can be empty or contain banner settings."
      }
    }
  },
  "customerDomain": {
    "type": "string | null",
    "meaning": "Custom external domain assigned to the company."
  }
}
```

