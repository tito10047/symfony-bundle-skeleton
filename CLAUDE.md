# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

A **Composer project template**, not a bundle with source code. It is published as
`tito10047/symfony-bundle-skeleton` and consumed with:

```bash
composer create-project tito10047/symfony-bundle-skeleton <directory>
```

All generation is done by the Composer plugin [`tito10047/symfony-bundle-maker`](https://github.com/tito10047/symfony-bundle-maker),
required in `require` (so `--no-dev` still runs it) and allowed in `config.allow-plugins`. On `post-create-project-cmd` it
asks its questions (or reads the `BUNDLE_MAKER_*` presets), removes the skeleton's own files
(`README.md`, `LICENSE`, `.github`, `.gitattributes`, `CLAUDE.md`), generates the bundle, rewrites
`composer.json`, runs one `composer update` and removes itself.

Generated bundles support Symfony `^7.4.17 || ^8.1.5` (PHP 8.2+ for 7.4, 8.4+ for 8.x): the first
patch releases whose ErrorHandler no longer leaks its exception handler into the tests. Symfony 8.0
is no longer maintained and not supported.

## Files

- `composer.json`: the only file that matters to the plugin. `require` is the minimum the
  plugin rewrites anyway (`php >=8.2`, `symfony/http-kernel ^7.4.17 || ^8.1.5`) plus
  `tito10047/symfony-bundle-maker ^1.0`. The plugin replaces `require` and `require-dev`
  with the bundle's own, so it removes itself.
- `.gitignore`, `LICENSE`, `README.md`: shipped in the dist archive.
- `.gitattributes`: export-ignores `.github/`, `CLAUDE.md` and itself so `create-project`
  does not ship them.
- `.github/workflows/ci.yml`: lint (`composer validate`, actionlint) and the end-to-end test,
  which runs the plugin's `tests/e2e/run.sh`.

## Rules when editing

- Changes to the generated bundle (layout, files, questions, dependencies) belong in
  `symfony-bundle-maker`, not here.
- **Keep `composer.json` minimal.** The plugin's `ComposerJsonWriter` replaces the keys it owns
  (name, description, license, type, keywords, authors, require, require-dev, autoload, scripts)
  and **keeps every other key** in the generated bundle. Never add `minimum-stability`,
  `prefer-stable`, `repositories`, `extra`, `homepage`, etc. here: they would leak into every
  generated bundle. The e2e test fails on `repositories`, `minimum-stability` and `prefer-stable`.
- `composer.json` must pass `composer validate --strict`; `sort-packages` is on, keep lists sorted.
- No `composer.lock`, no `vendor/` (gitignored).
- A new repository-only file must be export-ignored in `.gitattributes` (and the plugin's
  `BundleCreator::SKELETON_FILES` should remove it too).

## Testing

```bash
composer validate --strict
docker run --rm -v "$PWD:/repo" -w /repo rhysd/actionlint

# End-to-end: create-project from this checkout with a local symfony-bundle-maker checkout,
# then the bundle's "composer qa", as created.
E2E_SKELETON_DIR=$PWD BUNDLE_MAKER_NAME=acme/blog-bundle BUNDLE_MAKER_AUTHOR_NAME="Jane Doe" BUNDLE_MAKER_ALL=1 \
  ../symfony-bundle-maker/tests/e2e/run.sh /tmp/blog-bundle
```

`tests/e2e/run.sh` of the plugin (documented in its header and in the plugin's CONTRIBUTING.md)
copies this checkout (tracked and untracked files, nothing is written to it), exports it with
`git archive` from a temporary repository (export-ignore applied, and it fails if `.gitattributes`,
`.github` or `CLAUDE.md` would be shipped), declares both packages as path repositories in a
temporary `COMPOSER_HOME`, so the skeleton `composer.json` is used unmodified. It then checks the
generated `composer.json` (no leaked keys, no plugin reference), that the bundle's Composer plugins
ran, `composer validate --strict`, `dump-autoload --strict-psr`, `php -l`, and runs `composer qa`.

CI runs it with every feature on PHP 8.2 (Symfony 7.4) and 8.5 (Symfony 8.x). The plugin is
checked out from `BUNDLE_MAKER_REPOSITORY`@`BUNDLE_MAKER_REF` (workflow_dispatch inputs or
repository variables, default `<owner>/symfony-bundle-maker`@`main`). The plugin's own CI covers
the other configurations (formats, lowest dependencies).
