# Node Puppet Module for Boxen

[![Build Status](https://travis-ci.org/boxen/puppet-nodejs.png?branch=master)](https://travis-ci.org/boxen/puppet-nodejs)

Requires the following boxen modules:

* `boxen >= 3.2.0`
* `repository >= 2.1`
* [ripienaar/puppet-module-data](https://github.com/ripienaar/puppet-module-data)

## About

This module supports nove version management with either nodenv from [OiNutter](http://github.com/OiNutter/nodenv).
This module is heavily based on the official boxen's [ruby module](http://github.com/boxen/puppet-ruby)
All node versions are installed into `/opt/nodes`.

## About node-build version

Occasional bumps to the default node-build version are fine, on this module, but not essential.
The node-build version is something you should be managing in your own boxen repository,
rather than depending on this module to update for you. See examples on how to change the node-build
version in the Hiera section.

You can find a release list of versions for node-build [here](https://github.com/OiNutter/node-build/releases).

## Breakages since last major version

* nodes now live in /opt/nodes instead of /opt/boxen/nodenv/versions
* the module-data module is now **required**
* use of [OiNutter/nodenv](http://github.com/OiNutter/nodenv) instead of wfarr
* use of [OiNutter/node-build](http://github.com/OiNutter/node-build)
* npm packages are installed with `npm_module` instead of `nodejs::module`
* node versions are defined without the leading `v` (`0.10.36` instead of `v0.10.36`)

## Usage

```puppet
# Set the global default node (auto-installs it if it can)
class { 'nodejs::global':
  version => '0.12'
}

# ensure a certain node version is used in a dir
nodejs::local { '/path/to/some/project':
  version => '0.12'
}

# ensure a npm module is installed for a certain node version
# note, you can't have duplicate resource names so you have to name like so
$version = "0.12"
npm_module { "bower for ${version}":
  module       => 'bower',
  version      => '~> 1.4.1',
  node_version => $version,
}

# ensure a module is installed for all ruby versions
npm_module { 'bower for all nodes':
  module       => 'bower',
  version      => '~> 1.4.1',
  node_version => '*',
}

# install a node version
nodejs::version { '0.12.2': }
```

## Hiera configuration

The following variables may be automatically overridden with Hiera:

``` yaml
---

"nodejs::provider": "nodenv"
"nodejs::user": "deploy"

"nodejs::build::ensure": "f18b3d67756d1cb25ba6e35044f816fd67211b33"
"nodejs::nodenv::ensure": "v0.2.0"

# Environment variables for building specific versions
# You'll want to enable hiera's "deeper" merge strategy
# See http://docs.puppetlabs.com/hiera/1/configuring.html#mergebehavior
"nodejs::version::env":
  "0.4.2":
    "CC": "gcc"

# Version aliases, commonly used to bless a specific version
# Use the "deeper" merge strategy, as with nodejs::version::env
"nodejs::version::alias":
  "0.10": "0.10.36"
  "0.12": "0.12.0"
  "iojs-1.6": "iojs-1.6.2"
```

It is **required** that you include
[ripienaar/puppet-module-data](https://github.com/ripienaar/puppet-module-data)
in your boxen project, as this module now ships with many pre-defined versions
and aliases in the `data/` directory. With this module included, those
definitions will be automatically loaded, but can be overridden easily in your
own hierarchy.

You can also use JSON if your Hiera is configured for that.
