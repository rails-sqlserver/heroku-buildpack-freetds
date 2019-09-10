heroku-buildpack-freetds
=================================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks) for vendoring
the [FreeTDS](http://www.freetds.org/) binaries into your project.

### Note on Heroku config

Optionally, set the FreeTDS version in a Heroku config like this:

```bash
heroku config:set FREETDS_VERSION=1.00.109
```

Make sure the version you're referencing exists on as a `.tar.gz` file on the [FreeTDS releases](ftp://ftp.freetds.org/pub/freetds/stable/).

See `load_env_vars` in [`bin/compile`](bin/compile) for additional configurable variables.

You may also force the buildpack to rebuild the binary. This may be necessary when updating the buildpack.

```bash
heroku config:set FREETDS_REBUILD=true
```

Just don't forget, after a successful release, to unset it.

```bash
heroku config:unset FREETDS_REBUILD
```

## How it works

Rather than pulling down binary dependencies from a package manager and extracting them into place,
this buildpack compiles FreeTDS from source the first time an app is built with it.
The compiled binaries are cached for future builds, so this penalty is only incurred once.

This has the downside of a (potentially very long) deploy time for the first push,
with the benefit of a less-fragile build product that's somewhat less likely to break due to platform and dependency shifts.
Or at least, that's the hope!

## Stack compatibility

This buildpack is tested primarily against the `cedar-18` stack.

Allows for usage of [TinyTDS](https://github.com/rails-sqlserver/tiny_tds) on Heroku.

## Usage

This is used alongside the Ruby [buildpack](https://github.com/heroku/heroku-buildpack-ruby).

It may be useful to load any client libraries as part of the release task to ensure the libraries
linked correctly. For example, in a ruby app, you may add to the Procfile: `release: bundle exec ruby -e "p(require 'tiny_tds')"`

## Install

Use master

```bash
heroku buildpacks:set --index 1 https://github.com/rails-sqlserver/heroku-buildpack-freetds
```

or use a stable tag, like [v1.1.2](https://github.com/rails-sqlserver/heroku-buildpack-freetds/tree/v1.1.2)

```bash
heroku buildpacks:set --index 1 https://github.com/rails-sqlserver/heroku-buildpack-freetds#v1.1.2
```

## Changelog

- HEAD. [Diff](https://github.com/rails-sqlserver/heroku-buildpack-freetds/compare/v1.1.2...master)
- 1.1.2 Update FREETDS_VERSION from 1.00.21 to 1.00.109; Get source from https. [Diff](https://github.com/rails-sqlserver/heroku-buildpack-freetds/compare/v1.1.1...v1.1.2)
- 1.1.1 Fixed build linking. [Diff](https://github.com/rails-sqlserver/heroku-buildpack-freetds/compare/v1.1.0...v1.1.1)
- 1.1.0 Some improvements, including logging and env var FREETDS_REBUILD. [Diff](https://github.com/rails-sqlserver/heroku-buildpack-freetds/compare/v1.0.0...v1.1.0)
- 1.0.0 First stable release. [Diff](https://github.com/rails-sqlserver/heroku-buildpack-freetds/compare/d17ff27906644d0581e0654cd337562c20dcafe9...v1.0.0)

## Debugging

From 'heroku run bash'.

Print FreeTDS build logs to the screen:

```bash
for file in freetds/build_log-*; do echo -e "\n\n----------------------- $file------------------\n\n"; cat "$file"; done
```

Print TinyTDS build logs to the screen:

```bash
# change env vars as needed
( export TINY_TDS_VERSION="2.1.0"; export RUBY_PATH_VERSION="2.5.3"; for cmd in "ld /app/vendor/bundle/ruby/2.5.0/gems/tiny_tds-${TINY_TDS_VERSION}/lib/tiny_tds/tiny_tds.so" \
"cat /app/vendor/bundle/ruby/${RUBY_PATH_VERSION}/gems/tiny_tds-${TINY_TDS_VERSION}/ext/tiny_tds/Makefile" \
"cat /app/vendor/bundle/ruby/${RUBY_PATH_VERSION}/extensions/x86_64-linux/${RUBY_PATH_VERSION}-static/tiny_tds-${TINY_TDS_VERSION}/gem_make.out" \
"cat   /app/vendor/bundle/ruby/${RUBY_PATH_VERSION}/extensions/x86_64-linux/${RUBY_PATH_VERSION}-static/tiny_tds-${TINY_TDS_VERSION}/mkmf.log" ; do
echo -e "\n\n----------------------- $cmd------------------\n\n"; eval "$cmd"; done ; )
```

License
-------

'heroku-buildpack-freetds' is distributed under the MIT license.
FreeTDS http://www.freetds.org/ which is licensed under the GNU LGPL license at http://www.gnu.org/licenses/lgpl-2.0.html
