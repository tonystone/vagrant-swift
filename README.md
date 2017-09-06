# Vagrant Swift ![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-lightgray.svg?style=flat)

A parameterized Vagrant file to create a Swift development environment on Linux with either a stable release or a dev snapshot of the swift toolchain and libraries.

## Setup

> Please see [HashiCorp Vagrant Documentation](https://www.vagrantup.com) for help on getting Vagrant setup on your machine.

## Usage

By default, the vagrant script will use the latest release of Swift (currently 3.1.1.).  If this is your only requirement, simply call vagrant up as you normally would.

```
   > vagrant up
```

To use a particular release, use the following syntax.

```
   > vagrant --build-type=RELEASE --swift-version="2.2.1" up
```
 
For a development snapshot, use the following syntax specifying the date version of the snapshot.

```
   > vagrant --build-type=SNAPSHOT --swift-version=2017-09-05-a up
```

> Note: all vagrant specific parameters should come after the script parameters "--build-type" and "--swift-version".

## License

Vagrant Swift is released under the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html)

