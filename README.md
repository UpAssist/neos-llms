# UpAssist.Neos.Llms

Serves `/llms.txt` from your Neos CMS site so AI agents can discover and navigate the site's content.

[`llms.txt`](https://llmstxt.org) is a plain-text index of a site's documents, analogous to `robots.txt` but aimed at LLMs. This package turns the Neos content tree into one, honours `noindex` / hidden flags, and lets editors opt pages out per-node.

## Features

- Renders `/llms.txt` directly from the content tree — no static file to maintain.
- Auto-excludes nodes with `metaRobotsNoindex`, `_hidden`, or the package's `llmsTxtHidden` flag.
- Per-site description (editable on the Home node).
- 1h output cache, invalidated by content changes.

## Installation

Works on **both Neos 8 and Neos 9** — pick the constraint that matches your Neos version:

| Neos version | Composer constraint     | Branch     |
| ------------ | ----------------------- | ---------- |
| Neos 8.x     | `^1.0` or `dev-neos-8`  | `neos-8`   |
| Neos 9.x     | `^0.3` or `dev-main`    | `main`     |

This branch targets **Neos 9**. Only difference from the Neos 8 branch: the route part handler interface namespace is `Neos\Neos\FrontendRouting\` (Neos 8 uses `Neos\Neos\Routing\`). Everything else — mixin, Fusion helper, caching, translations, per-page `llmsTxtDescription` with `metaDescription` fallback — is shared 1:1.

```bash
composer require upassist/neos-llms:^0.3
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
        "upassist/neos-llms": "^0.3"
    }
}
```

## Usage

### 1. Attach the mixin to your Document NodeTypes

Apply the mixin to the Document NodeTypes you want to include in `llms.txt`. The mixin adds:

- `llmsTxtHidden: boolean` — per-page opt-out checkbox under the *AI / LLMs* inspector group.
- `llmsTxtDescription: string` (TextArea) — only relevant on your site/home node; used as the blockquote under the site title.

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
- `_hidden: true` (Neos "hidden" flag)
- `metaRobotsNoindex: true` (Neos.Seo noindex)

This means setting a page to `noindex` for search engines also removes it from `llms.txt` — no double bookkeeping.

## Output

```
# {Site Title}

> {llmsTxtDescription}

## Pages

- [Page Title](https://example.com/page): metaDescription truncated to 200 chars
- ...
```

Served with `Content-Type: text/plain; charset=utf-8`. Cached for 1 hour, tagged `Everything` so regular content publishes invalidate it.

## Customising the "Pages" heading

Override the Fusion prototype to change the heading label:

```fusion
prototype(UpAssist.Neos.Llms:Helper.LlmsTxt) {
    pagesHeading = 'Documents'
}
```

## License

MIT

## Credits

Originally prototyped for [ewijksolartechniek.nl](https://github.com/UpAssist). Extracted into a reusable package to power AI discoverability across the UpAssist portfolio.
