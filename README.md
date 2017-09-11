# Vagrant Swift ![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-lightgray.svg?style=flat)

A parameterized Vagrant file to create a Swift development environment on Linux with either a stable release or a dev snapshot of the swift toolchain and libraries.

## Vagrant Setup

> Please see [HashiCorp Vagrant Documentation](https://www.vagrantup.com) for help on getting Vagrant setup on your machine.

## Swift Build Information

> Information about available Swift builds and supported **Ubuntu** versions for each build can be obtained from the swift.org site at [http://swift.org/download](http://swift.org/download).


Vagrant Swift supports all builds from the `swift.org/download` site including:

- Releases
- Trunk Development (master)
- Swift 4.0 Development
- Swift 3.1 Development
- Older Release Branches (which include all Swift 2.2 builds)

## Usage

The vagrant script has been parameterized with 3 **optional** parameters:
```
--swift-release
--platform-version
--build
```
The `--swift-release` parameter accepts a RELEASE version number in the form of `#.#[.#]`.

The `--platform-version` accepts the **ubuntu** version of the VM you would like start.  This must match one of the versions supported by the Swift builds on `swift.org/download` site.  The version must be in the form of `##.##` (for example `16.04` or `16.10`.)

> **Note**: All Swift builds are currently built for `ubuntu` so there is currently no option to change the OS.

The `--build` parameter can be used to create an environment from a build listed on the `swift.org/download` site.  You can specify the entire link to the build or just the last path component or build name.


By default, the vagrant script will use the latest release of Swift (currently 3.1.1.) and Ubuntu 14.04 if no parameters are passed.  If this is your only requirement, simply call `vagrant up` as you normally would.

```
   > vagrant up
```

To use a particular release, use the following syntax.

```
   > vagrant --swift-release=3.0 up
```
And to also supply the platform-version.

```
   > vagrant --platform-version=16.10 --swift-release=3.0 up
```
> **Note**: `--platform-version` and `--swift-release` can be specified individulally.

For a development trunk or branch snapshot, use the following syntax specifying the build information obtained from the `swift.org` website for the build.

```
   > vagrant --build='swift-4.0-DEVELOPMENT-SNAPSHOT-2017-09-07-a-ubuntu16.04' up
```
You may also pass the entire path to the `--build` parameter as in the following example.

```
   > vagrant --build='https://swift.org/builds/swift-4.0-branch/ubuntu1604/swift-4.0-DEVELOPMENT-SNAPSHOT-2017-09-07-a/swift-4.0-DEVELOPMENT-SNAPSHOT-2017-09-07-a-ubuntu16.04.tar.gz' up
```

> **Note**: all vagrant specific parameters should come after the script parameters `--swift-release`, `--platform-version` and `--build`.

## License

Vagrant Swift is released under the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html)