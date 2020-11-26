baschny/action-php-composer
===========================

This GitHub action allows to run the latest composer on your source code.

It uses the official composer docker image and allows efficient caching of
downloaded assets.

Input
-----
* `composer_version`: Major version to install. Basically any tag name from https://hub.docker.com/_/composer -
   i.e. `1` or `2`. Defaults to `latest`.

* `command`: The composer command to run, defaults to `install`. Set to `get_config` to only generate the output
   variables of the composer setup (for use in cache, see example below)

* `args`: Other command line arguments to pass to composer, defaults to `--optimize-autoloader --no-progress`

Output
------
* `composer_cache_dir`: where the composer cache is located, in case you want to use `actions/cache`

* `composer_major_version`: the detected major version of composer, useful to have a separate cache tag based on
  this, since there can be slight differences in the caches between major versions.

* `composer_version`: the exact composer version, in case you want to output this somewhere.

Example Workflow
----------------

```yaml
name: PHP Building Workflow

on: [push]

jobs:

  composer_install:
    runs-on: ubuntu-latest

    env:
      COMPOSER_VERSION: 1

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Get composer cache directory
        id: composer_config
        uses: baschny/action-php-composer
        with:
          composer_version: ${{ env.COMPOSER_VERSION }}
          command: get_config

      - name: Cache composer downloads
        uses: actions/cache@v2
        with:
          path: ${{ steps.composer_config.outputs.composer_cache_dir }}
          key: composer-v${{ steps.composer_config.outputs.composer_major_version }}

      - name: Run composer install
        uses: baschny/action-php-composer
        with:
          composer_version: ${{ env.COMPOSER_VERSION }}
          command: install
```

License
-------

See LICENSE file.

See also
--------

The idea came after some brainstorming in https://github.com/php-actions/composer.

Author
------

Ernesto Baschny, cron IT GmbH