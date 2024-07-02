# Targito Tracking — Google Tag Manager Template

Official Google Tag Manager custom tag template for [Targito](https://www.targito.com) tracking.

## Overview

This template loads the Targito tracking script and sends tracking events from your website through Google Tag Manager. It replaces the manual JavaScript snippet installation described in the [Targito Tracking Documentation](https://app.targito.com/cs/config/tracking/).

## Tag Types

### Initialization (Load Tracking Script)

Loads the Targito `tr.js` tracking library onto the page. This tag should fire **once on all pages** (use an "All Pages" trigger).

**Required fields:**

| Field | Description | Example |
|-------|-------------|---------|
| Account ID | Your Targito account UUID v4 | `ec008fe3-19d3-4cdc-85f5-3bdf457c3ca8` |
| Origin | Your Targito origin identifier | `smarty_cz` |
| Tracking Domain | Your verified tracking domain | `targito.smarty.cz` |

### Track Event

Sends specific tracking events to Targito. Create separate tags for each event type and use appropriate triggers.

**Supported event types:**

| Event Type | Description | Required Fields |
|------------|-------------|-----------------|
| Page View (SPA) | Manual page view for single-page apps | — |
| Product View | User views a product page | Item ID |
| Product Group View | User views a product group/variant | Item ID |
| Article View | User views an article | Item ID |
| Category View | User views a category page | Item ID |
| Brand View | User views a brand page | Item ID |
| Cart Update | Cart contents change (add/remove/update) | Total, Total VAT, Products |
| Order Completion | Purchase completed (thank-you page) | Order ID, Total, Total VAT, Products |
| Pair User | Link logged-in user to Targito contact | Targito Contact ID |
| Identify User | Set Targito ID cookie for logged-in user | Targito Contact ID |

## Setup Instructions

### Step 1: Add the Initialization Tag

1. In GTM, create a new tag using the **Targito Tracking** template
2. Set Tag Type to **Initialization (Load Tracking Script)**
3. Enter your Account ID, Origin, and Tracking Domain
4. Set the trigger to **All Pages**
5. Set tag firing priority higher than event tags (e.g., priority 100)

### Step 2: Add Event Tags

Create additional tags for each event you want to track:

**Product View example:**
1. Create a new **Targito Tracking** tag
2. Set Tag Type to **Track Event**
3. Set Event Type to **Product View**
4. Enter the Item ID (use a GTM variable like `{{Product ID}}`)
5. Set trigger to fire on product detail pages

**Cart Update example:**
1. Create a new **Targito Tracking** tag
2. Set Tag Type to **Track Event**
3. Set Event Type to **Cart Update**
4. Fill in Total, Total VAT, and Products using GTM variables
5. Set trigger to fire on cart change events

### Products Variable Format

For Cart Update and Order Completion events, the **Products** field expects a GTM variable that resolves to an array of product objects:

```json
[
  {
    "item_id": "391202",
    "item_price": 11561.98,
    "item_price_vat": 13990.00,
    "item_quantity": 2
  },
  {
    "item_id": "101729",
    "item_price": 100.00,
    "item_price_vat": 121.00,
    "item_quantity": 1
  }
]
```

### Custom Attributes

The optional **Custom Attributes** field accepts a GTM variable that resolves to an object with additional metadata:

```json
{
  "billing_full_name": "Petr Novák",
  "billing_city": "Praha",
  "discount_code": "SUMMER2025"
}
```

## Technical Notes

- The template uses the GTM `injectScript` API to load the Targito tracking library
- Global configuration is passed via `window._targitoConfig` since the GTM sandbox does not support setting data attributes on injected script elements
- Events use `Targito.push()` which queues calls before the script loads and processes them immediately after
- The **Pair User** and **Identify User** events should only fire at the moment of login, not on every page load

## Permissions

This template requires the following GTM permissions:

- **Injects scripts** — loads `https://cdn.<domain>/tr.js`
- **Accesses global variables** — reads/writes `Targito` queue, writes `_targitoConfig`, executes `Targito.push` and `Targito.pageview`
- **Logs to console** — debug logging in preview mode only

## Support

For questions about implementation, contact your Targito project manager or visit [targito.com](https://www.targito.com).

## License

Apache License 2.0 — see [LICENSE](LICENSE).
