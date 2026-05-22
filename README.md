# Complianz GDPR (Unie van Waterschappen fork)

## Why we run a fork

Upstream Complianz registers its Gutenberg blocks (`complianz/document`,
`complianz/consent-area`) without declaring `apiVersion: 3`. Per the
[Block API Versions reference][block-api-versions], the post editor only
runs inside an iframe when **all** registered blocks are v3 or higher.
The Complianz blocks therefore force the editor out of the iframe, which
breaks the block-editor assets in our `waterschappen-*` themes (they
assume the iframed editor).

WordPress 7.0 will iframe the editor unconditionally, regardless of any
block's `apiVersion`. Once we're on 7.0 this fork can be retired and the
plugin installed via Composer (e.g. `wpackagist-plugin/complianz-gdpr`).

## What the fork does

A one-line patch in `complianz-gpdr.php` (`Complianz::includes()`) that
skips registering the Complianz Gutenberg blocks:

```php
/* Gutenberg block */
$use_gutenberg = false;
if (cmplz_uses_gutenberg() && $use_gutenberg) {
  require_once plugin_dir_path(__FILE__) . "gutenberg/block.php";
}
```

With `gutenberg/block.php` never loaded, no non-v3 blocks are registered,
so the editor iframes itself normally.
