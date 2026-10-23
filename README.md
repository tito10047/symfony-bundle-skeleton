# Symfony Bundle Skeleton

Create a new Symfony bundle in seconds. An [assistant](https://github.com/tito10047/symfony-bundle-maker) asks
a few questions and generates a bundle that follows the
[Symfony bundle best practices](https://symfony.com/doc/current/bundles/best_practices.html):
tested, documented and ready to publish.

## Requirements

- PHP 8.2 or higher
- Composer 2.6 or higher

Generated bundles support these Symfony versions:

| Symfony | PHP |
|---|---|
| 7.4 (LTS), from 7.4.17 | 8.2+ |
| 8.1, from 8.1.5, and later 8.x versions | 8.4+ |

## Quick start

```console
$ composer create-project tito10047/symfony-bundle-skeleton acme-blog-bundle
```

The assistant asks its questions, shows a summary of the names it derived and asks for a
confirmation (answer no to start over with your answers as defaults). It then generates the
bundle, installs its dependencies with a single `composer update` and removes itself: the
bundle does not depend on the skeleton.

The assistant is a Composer plugin installed with the skeleton (also with `--no-dev`): it does
not run with `--no-plugins` or `--no-install`.

## Questions

### Identity and configuration

| Question | Default |
|---|---|
| Package name (`vendor/name`) | `<GitHub user or "acme">/<directory name>-bundle` |
| Description | `<BundleClass> for Symfony` |
| Author name | `user.name` of your Git configuration |
| Author email (`none` omits it) | `user.email` of your Git configuration |
| License: `MIT`, `Apache-2.0`, `BSD-2-Clause`, `BSD-3-Clause`, `GPL-3.0-or-later`, `LGPL-3.0-or-later`, `MPL-2.0` or `proprietary` | `MIT` |
| Configuration format of services and routes: `php` (fluent configurators), `php-array` (PHP arrays) or `yaml` | `php` |

Every name is derived from the package name:

| | `acme/blog-bundle` |
|---|---|
| Namespace | `Acme\BlogBundle` |
| Bundle class | `AcmeBlogBundle` |
| Extension alias, prefix of service ids and parameters | `acme_blog` |
| Translation domain and Twig namespace | `AcmeBlog` |
| Slug: command prefix, CSS classes, file names | `acme-blog` |

The examples below use `acme/blog-bundle`. `<ext>` is the extension of the chosen
configuration format: `php` for `php` and `php-array`, `yaml` for `yaml`.

### Features

Each feature comes with its tests and a section in `docs/index.md`.

| Feature | Default | Generates |
|---|---|---|
| Semantic configuration | yes | `config/definition.php` (a `greeting` option, passed to the greeter service), loaded by the bundle class |
| Controller | no | `src/Controller/HelloController.php`, an invokable controller, and `config/routes.<ext>` with the `acme_blog_hello` route (`GET /hello/{name}`), to import in applications |
| Twig template | yes with a controller | `templates/hello.html.twig` in the `@AcmeBlog` namespace, extending no layout |
| Translations | no | `translations/AcmeBlog.en.xlf` (XLIFF), in the `AcmeBlog` domain |
| Console command | no | `src/Command/HelloCommand.php`, an invokable `acme-blog:hello` command, loaded lazily |
| Event listener | no | `src/EventListener/GreetingHeaderListener.php`, adding an `X-Acme-Blog-Greeting` header on `kernel.response` when the `greeting_header` option is enabled (off by default); requires the semantic configuration |
| Doctrine entity | no | `src/Entity/Greeting.php` mapped in XML by `config/doctrine/Greeting.orm.xml` (`acme_blog_greeting` table), registered by a compiler pass when the application uses the ORM, in the entity manager of the `model_manager_name` option |
| Public assets | no | `public/css/acme-blog.css`, installed with `assets:install` |
| Symfony UX | no | a Stimulus controller, `assets/package.json` and `assets/src/hello_controller.js`, exposed to AssetMapper |

Features work together: with a controller, the template is rendered by it, and the template
uses the translations, the stylesheet and the Stimulus controller when they are generated.

### Quality assurance

| Feature | Default | Generates |
|---|---|---|
| PHPStan | yes | `phpstan.dist.neon`: level 8, with the Symfony and PHPUnit extensions (and the Doctrine one when used) |
| PHP-CS-Fixer | yes | `.php-cs-fixer.dist.php`: the `@Symfony` and `@Symfony:risky` rule sets, as Symfony itself |
| Rector | yes | `rector.php`: the upgrade sets of the lowest supported versions (PHP 8.2, Symfony 7.4, PHPUnit 11.5), a local tool: not part of `composer qa` nor of the CI |
| GitHub Actions | yes | `.github/workflows/ci.yml` and `.github/dependabot.yml` |

## Without interaction

Every answer can be preset with an environment variable. With `--no-interaction` presets
are used as is, otherwise they are proposed as default answers.

```console
$ BUNDLE_MAKER_NAME=acme/blog-bundle BUNDLE_MAKER_CONFIG_FORMAT=yaml BUNDLE_MAKER_ALL=1 \
  composer create-project tito10047/symfony-bundle-skeleton acme-blog-bundle --no-interaction
```

| Variable | Value |
|---|---|
| `BUNDLE_MAKER_NAME` | package name, e.g. `acme/blog-bundle` |
| `BUNDLE_MAKER_DESCRIPTION` | package description |
| `BUNDLE_MAKER_AUTHOR_NAME` | author name |
| `BUNDLE_MAKER_AUTHOR_EMAIL` | author email, `none` to omit it |
| `BUNDLE_MAKER_LICENSE` | `MIT`, `Apache-2.0`, `BSD-2-Clause`, `BSD-3-Clause`, `GPL-3.0-or-later`, `LGPL-3.0-or-later`, `MPL-2.0` or `proprietary` |
| `BUNDLE_MAKER_CONFIG_FORMAT` | `php`, `php-array` or `yaml` |
| `BUNDLE_MAKER_ALL` | `1` enables every feature, `0` disables them all |
| `BUNDLE_MAKER_<FEATURE>` | `1` or `0` for a single feature, takes precedence over `BUNDLE_MAKER_ALL` |

Feature variables:

| Features | Quality assurance |
|---|---|
| `BUNDLE_MAKER_CONFIG`, `BUNDLE_MAKER_CONTROLLERS`, `BUNDLE_MAKER_TWIG`, `BUNDLE_MAKER_TRANSLATIONS`, `BUNDLE_MAKER_COMMAND`, `BUNDLE_MAKER_EVENT_LISTENER`, `BUNDLE_MAKER_DOCTRINE`, `BUNDLE_MAKER_PUBLIC_ASSETS`, `BUNDLE_MAKER_UX` | `BUNDLE_MAKER_PHPSTAN`, `BUNDLE_MAKER_CS_FIXER`, `BUNDLE_MAKER_RECTOR`, `BUNDLE_MAKER_CI` |

Booleans also accept `true`/`false`, `yes`/`no`, `y`/`n` and `on`/`off`. Empty variables are
ignored, invalid values stop the assistant with an error.

## Example

`acme/blog-bundle` with every feature and the `php` format:

```
acme-blog-bundle/
├── .github/
│   ├── workflows/ci.yml
│   └── dependabot.yml
├── assets/
│   ├── src/hello_controller.js
│   └── package.json
├── config/
│   ├── doctrine/Greeting.orm.xml
│   ├── definition.php
│   ├── routes.php
│   └── services.php
├── docs/index.md
├── public/css/acme-blog.css
├── src/
│   ├── Command/HelloCommand.php
│   ├── Controller/HelloController.php
│   ├── Entity/Greeting.php
│   ├── EventListener/GreetingHeaderListener.php
│   ├── Exception/
│   │   ├── ExceptionInterface.php
│   │   └── InvalidArgumentException.php
│   ├── AcmeBlogBundle.php
│   ├── Greeter.php
│   └── GreeterInterface.php
├── templates/hello.html.twig
├── tests/
│   ├── App/TestKernel.php
│   ├── Functional/          the controller, through the test kernel
│   ├── Integration/         the bundle, its configuration, command, mapping, template...
│   ├── Unit/                the greeter and the event listener
│   └── object-manager.php   the entity manager of the PHPStan Doctrine extension
├── translations/AcmeBlog.en.xlf
├── .editorconfig
├── .gitattributes
├── .gitignore
├── .php-cs-fixer.dist.php
├── CHANGELOG.md
├── composer.json
├── LICENSE
├── phpstan.dist.neon
├── phpunit.dist.xml
├── README.md
└── rector.php
```

With `yaml`, `config/services.php` and `config/routes.php` become `config/services.yaml` and
`config/routes.yaml`. `config/definition.php` is always PHP.

## What you get out of the box

Every bundle follows the [best practices](https://symfony.com/doc/current/bundles/best_practices.html):

- **Bundle class** extending `AbstractBundle`, with no `DependencyInjection/` directory.
- **Explicit services**: no autowiring nor autoconfiguration, private, with ids prefixed by
  the extension alias (`acme_blog.greeter`) and aliases for autowiring (`GreeterInterface`).
- **A working example**: a greeter service, its interface and the exceptions of the bundle,
  used by the controller, the command and the event listener.
- **Standard files**: `README.md` with the standard installation instructions, `docs/index.md`,
  `LICENSE` (full text of the chosen license), `CHANGELOG.md`, `.editorconfig`, `.gitignore`
  and `.gitattributes` (development files are excluded from the archive installed by Composer).
- **A complete `composer.json`**: `symfony-bundle` type, PSR-4 autoloading, Symfony
  dependencies constrained to `^7.4.17 || ^8.1.5` and a Composer script for each tool.
- **PHPUnit tests** with a test kernel, fully covering the generated code and failing on
  deprecations triggered by the bundle.
- **Continuous integration** (GitHub Actions) on pushes to `main`, pull requests and every week:
  - tests on PHP 8.2 (lowest dependencies) and 8.3 with Symfony 7.4, PHP 8.4 with Symfony 8.1
    (lowest dependencies), PHP 8.5 with Symfony 7.4 and 8.1, and the next minor version of
    Symfony, allowed to fail;
  - code coverage with PHP 8.5 and Symfony 8.1, failing below 95% of the lines;
  - static analysis and coding standards.

In the generated bundle:

```console
$ composer test   # runs the tests
$ composer qa     # runs the tests and every quality check
```

`composer cs:fix` applies the coding standards, `composer rector` and `composer rector:fix` check
and apply the refactorings.
The `README.md` of the bundle lists every script.

## Learn more

- [Best Practices for Reusable Bundles](https://symfony.com/doc/current/bundles/best_practices.html)
- [The Bundle System](https://symfony.com/doc/current/bundles.html)
- [How to Create Friendly Configuration for a Bundle](https://symfony.com/doc/current/bundles/configuration.html)
- [How to Override any Part of a Bundle](https://symfony.com/doc/current/bundles/override.html)
- [Create a UX Bundle](https://symfony.com/doc/current/frontend/create_ux_bundle.html)
- [The assistant (tito10047/symfony-bundle-maker)](https://github.com/tito10047/symfony-bundle-maker), to report issues with the generated code or to contribute

## Credits

Based on [yceruto/bundle-skeleton](https://github.com/yceruto/bundle-skeleton) and
[yceruto/bundle-flex](https://github.com/yceruto/bundle-flex) by [Yonel Ceruto](https://github.com/yceruto).

## License

This software is published under the [MIT License](LICENSE).
