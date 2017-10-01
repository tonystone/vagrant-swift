# Change Log
All significant changes to this project will be documented in this file.

## [1.1.0](https://github.com/tonystone/vagrant-swift/releases/tag/1.1.0)

### Changes
- Updated default Swift version to Swift 4.0 Release.

### Added
- Added libcurl3 to common library install (now required by Swift 4.0 Release).

### Bug Fixes
- Fixed an issue where `--swift-release` and `--platform-version` could not be used independently.

## [1.0.0](https://github.com/tonystone/vagrant-swift/releases/tag/1.0.0)

Initial version of a parameterized Vagrant file to create a Swift development environment on Linux with either a stable release or a dev snapshot of the swift toolchain and libraries
