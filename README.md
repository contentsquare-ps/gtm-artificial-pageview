# Contentsquare Artificial Pageview Tag for GTM

A Google Tag Manager community template that fires artificial pageviews to the Contentsquare tag (`_uxa`). This is useful for tracking virtual page transitions in single-page applications, or for associating popup open/close events with distinct pageview paths in Contentsquare.

## Overview

Contentsquare tracks user journeys by pageview. When navigation does not produce a real browser page load (e.g. modals, SPAs, tab switches), you can use this tag to push a synthetic `trackPageview` event to `_uxa` — the Contentsquare data layer queue — along with an optional custom query string.

## Parameters

| Parameter | Description |
|---|---|
| **Artificial Pageview Value** | Free text appended to the current URL's query string before firing the pageview. Maximum 255 characters. Do not include `#`. Leave blank when using the **Close Popup** option. |
| **Choose Option** | Controls the format of the artificial pageview (see [Options](#options) below). |

## Options

| Option | Value written to `_uxa` | Notes |
|---|---|---|
| **Free text** | `?<your value>` appended to the current path | Default mode. Use any descriptive path segment. |
| **Opening of a popup** | `?cs-popin-<your value>` appended to the current path | Prefixes the value with `cs-popin-` so Contentsquare can identify popup-open events. |
| **Closing of a popup** | No query string modification | Sends a plain `trackPageview` for the current path with no extra query. Do not fill in a value. |

## How It Works

1. The tag reads the current page URL (`pathname`, `search`, `hash`).
2. If the option is not **Close Popup** and a value is provided, it calls `_uxa.push(["setQuery", ...])` with `lifespan: "onNextPageviewOnly"` to attach the custom query string to the next pageview only.
3. It then calls `_uxa.push(["trackPageview", pathname + "?__" + hash_fragment])` to fire the artificial pageview.

`#` characters in the hash are replaced with `?__` so Contentsquare receives the fragment as part of the path rather than having it stripped by the browser.

## Usage

### Free Text Example

Firing an artificial pageview for a checkout step:

- **Artificial Pageview Value**: `checkout-step-2`
- **Choose Option**: Free text

Result: Contentsquare records a pageview for `https://example.com/cart?checkout-step-2`.

### Popup Open Example

- **Artificial Pageview Value**: `newsletter-modal`
- **Choose Option**: Use for the opening of a popup

Result: Contentsquare records a pageview for `https://example.com/home?cs-popin-newsletter-modal`.

### Popup Close Example

- **Artificial Pageview Value**: *(leave blank)*
- **Choose Option**: Use for the closing of a popup

Result: Contentsquare records a pageview for `https://example.com/home` with no extra query string.

## Requirements

- Contentsquare tag must be present on the page and initialising the `_uxa` array before this tag fires.
- Container context: **Web**.

## Documentation

Full Contentsquare GTM integration guide: https://docs.contentsquare.com/en/web/#google-tag-manager-template
