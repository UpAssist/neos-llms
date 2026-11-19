# UpAssist.Neos.Llms

Serves `/llms.txt` from your Neos CMS site so AI agents can discover and navigate the site's content.

[`llms.txt`](https://llmstxt.org) is a plain-text index of a site's documents, analogous to `robots.txt` but aimed at LLMs. This package turns the Neos content tree into one, honours `noindex` / hidden flags, and lets editors opt pages out per-node.

## Features

- Renders `/llms.txt` directly from the content tree — no static file to maintain.
- Auto-excludes documents that are hidden in Neos, set to `metaRobotsNoindex`, or opted out with the package's `llmsTxtHidden` flag.
- Per-site description (editable on the Home node).
- 1h output cache, invalidated by content changes.

## Installation

Works on **both Neos 8 and Neos 9**, one major version per Neos line:

| Package version | Branch | Neos (tested)       | Composer constraint            |
| --------------- | ------ | ------------------- | ------------------------------ |
| 2.x             | `main` | 9.0, 9.1, 9.2       | `^2.0` (development: `dev-main`) |
| 1.x             | `1.x`  | 8.3, 8.4            | `^1.0` (development: `1.x-dev`)  |
| 0.x             | –      | 9.x, superseded by 2.x | –                           |

This branch targets **Neos 8**. Differences from the `main` (Neos 9) branch are in `Configuration/Routes.yaml` only: the route part handler interface namespace is `Neos\Neos\Routing\` (Neos 9 moved it to `Neos\Neos\FrontendRouting\`), and Neos 9 needs `uriPathSuffix: ''` on the route. Everything else — mixin, Fusion helper, caching, translations — is shared.

```bash
composer require upassist/neos-llms:^1.0
```

Or add a repository to your `composer.json`:

```json
{
    "repositories": [
        {
            "type": "git",
            "url": "git@github.com:UpAssist/neos-llms.git"
        }
    ],
    "require": {
        "upassist/neos-llms": "^1.0"
    }
}
```

## Usage

### 1. Attach the mixin to your Document NodeTypes

Apply the mixin to the Document NodeTypes you want to include in `llms.txt`. The mixin adds:

- `llmsTxtHidden: boolean` — per-page opt-out checkbox under the *AI / LLMs* inspector group.
- `llmsTxtDescription: string` (TextArea) — on the site/home node the blockquote under the site title, on other pages the description of the page entry (see step 3).

```yaml
'Your.Package:Document.Page':
  superTypes:
    'UpAssist.Neos.Llms:Mixin.LlmsTxt': true

'Your.Package:Document.Home':
  superTypes:
    'UpAssist.Neos.Llms:Mixin.LlmsTxt': true
```

### 2. Register the route

The package ships a `Routes.yaml`. Register it in your site package's `Configuration/Settings.yaml` so the `/llms.txt` URL is routed:

```yaml
Neos:
  Flow:
    mvc:
      routes:
        'UpAssist.Neos.Llms':
          position: 'before Neos.Neos'
```

### 3. Fill the descriptions

In the Neos backend, every document with the mixin exposes an *AI / LLMs* inspector group with a `llmsTxtDescription` textarea:

- **On the site/home node**, it becomes the `> blockquote` under the site title.
- **On other pages**, it becomes the per-page entry after the page title. If empty, the page entry falls back to `metaDescription`.

Prefer filling `llmsTxtDescription` per page when you want an AI-facing summary that differs from the SEO meta description (e.g. more factual, less marketing).

## Exclusion rules

A document does **not** appear in `llms.txt` when any of the following is true:

- `llmsTxtHidden: true` (per-page opt-out)
- the document is hidden in Neos ("Hide" in the backend)
- `metaRobotsNoindex: true` (Neos.Seo noindex)

This means setting a page to `noindex` for search engines also removes it from `llms.txt` — no double bookkeeping.

## Output

```
# {Site Title}

> {llmsTxtDescription}

## Pages

- [Page Title](https://example.com/page): {llmsTxtDescription, or metaDescription as fallback}
- ...
```

Served with `Content-Type: text/plain; charset=utf-8`. Cached for up to 1 hour; the entry is flushed when the site node or any document with the mixin changes.

## Customising the "Pages" heading

Override the Fusion prototype to change the heading label:

```fusion
prototype(UpAssist.Neos.Llms:Helper.LlmsTxt) {
    pagesHeading = 'Documents'
}
```

## License

MIT
