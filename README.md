heroku-buildpack-freetds
=================================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks) for vendoring
the [FreeTDS](http://www.freetds.org/) binaries into your project.

### Note on Heroku config

Optionally, set the FreeTDS version in a Heroku config like this:

```bash
heroku config:set FREETDS_VERSION=1.00.21
```

Make sure the version you're referencing exists on as a `.tar.gz` file on the [FreeTDS releases](ftp://ftp.freetds.org/pub/freetds/stable/).

See `load_env_vars` in [`bin/compile`](bin/compile) for additional configurable variables.

## How it works

Rather than pulling down binary dependencies from a package manager and extracting them into place,
this buildpack compiles FreeTDS from source the first time an app is built with it.
The compiled binaries are cached for future builds, so this penalty is only incurred once.

This has the downside of a (potentially very long) deploy time for the first push,
with the benefit of a less-fragile build product that's somewhat less likely to break due to platform and dependency shifts.
Or at least, that's the hope!

## Stack compatibility

This buildpack is tested primarily against the `cedar-14` stack.

Allows for usage of [TinyTDS](https://github.com/rails-sqlserver/tiny_tds) on Heroku.

## Usage

This is used alongside the Ruby [buildpack](https://github.com/heroku/heroku-buildpack-ruby).

## Install

```bash
heroku buildpacks:add --index 1 https://github.com/x-b-e/heroku-buildpack-freetds
```

License
-------

'heroku-buildpack-freetds' is distributed under the MIT license.
FreeTDS http://www.freetds.org/ which is licensed under the GNU LGPL license at http://www.gnu.org/licenses/lgpl-2.0.html
