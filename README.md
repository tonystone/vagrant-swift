# Vagrant Swift ![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-lightgray.svg?style=flat)

A parameterized Vagrant file to create a Swift development environment on Linux with either a stable release or a dev snapshot of the swift toolchain and libraries.

## Vagrant Setup

> Please see [HashiCorp Vagrant Documentation](https://www.vagrantup.com) for help on getting Vagrant setup on your machine.

## Swift Build Information

> Information about available Swift builds and supported **Ubuntu** versions for each build can be obtained from the swift.org site at [http://swift.org/download](http://swift.org/download).

## Usage

The vagrant script has been parameterized with 2 **optional** parameters:
```
--platform-version
--swift-version
```
The `platform-version` accepts the **ubuntu** version of the VM you would like start.  This must match one of the versions supported by the Swift builds on `swift.org/download` site.  The version must be in the form of `##.##` (for example `16.04` or `16.10`.)

> **Note**: All Swift builds are currently built for `ubuntu` so there is currently no option to change the OS.

The `swift-version` parameter accepts either a RELEASE version number in the form of `#.#[.#]` or a SNAPSHOT in the form of `####-##-##`.

By default, the vagrant script will use the latest release of Swift (currently 3.1.1.) and Ubuntu 16.04 if no parameters are passed.  If this is your only requirement, simply call `vagrant up` as you normally would.

```
   > vagrant up
```

To use a particular release, use the following syntax.

```
   > vagrant --swift-version=3.0 up
```

For a development snapshot, use the following syntax specifying the date version of the snapshot.

```
   > vagrant --swift-version=2017-09-05 up
```

And to also supply the platform-version.

```
   > vagrant --platform-version=16.10 --swift-version=2017-09-05 up
```

> **Note**: all vagrant specific parameters should come after the script parameters `--platform-version` and `--swift-version`.

## License

Vagrant Swift is released under the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html)