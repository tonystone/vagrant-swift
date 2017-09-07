#
#   Vagrantfile
#
#   Copyright 2016 Tony Stone
#
#   Licensed under the Apache License, Version 2.0 (the "License");
#   you may not use this file except in compliance with the License.
#   You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
#   Unless required by applicable law or agreed to in writing, software
#   distributed under the License is distributed on an "AS IS" BASIS,
#   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#   See the License for the specific language governing permissions and
#   limitations under the License.
#
#   Created by Tony Stone on 4/29/16.
#
require 'getoptlong'

#
# This script can be passed script arguments from the vagrant
# command line.  At this point script arguments must come
# before vagrant commands and arguments.
#
# Examples:
#
# > vagrant --build-type=SNAPSHOT --swift-version=2017-09-05-a up --provider=virtualbox
#
# > vagrant --build-type=RELEASE --swift-version="2.2.1" up --provider=virtualbox
#
:RELEASE
:SNAPSHOT

swift_version="3.1.1"
swift_platform="ubuntu14.04"
build_type=:RELEASE

options = GetoptLong.new(
    [ '--swift-version', GetoptLong::REQUIRED_ARGUMENT ],
    [ '--build-type'   , GetoptLong::REQUIRED_ARGUMENT ]
)
options.quiet = true

begin
  options.each do |option, value|
    if option == '--swift-version' then swift_version=value
    elsif option == '--build-type' then build_type=value.to_sym
    end
  end
rescue GetoptLong::InvalidOption
  pass
end

source_name=""
source_directory=""
swift_platform_dir=swift_platform.tr('.', '')

if build_type == :SNAPSHOT
  # Example:
  #
  # https://swift.org/builds/development/ubuntu1610/swift-DEVELOPMENT-SNAPSHOT-2017-09-05-a/swift-DEVELOPMENT-SNAPSHOT-2017-09-05-a-ubuntu16.10.tar.gz
  #
  source_directory = "https://swift.org/builds/development/#{swift_platform_dir}/swift-DEVELOPMENT-SNAPSHOT-#{swift_version}"
  source_name      = "swift-DEVELOPMENT-SNAPSHOT-#{swift_version}-#{swift_platform}"

else
  #
  # Example:
  #
  # https://swift.org/builds/swift-3.1.1-release/ubuntu1610/swift-3.1.1-RELEASE/swift-3.1.1-RELEASE-ubuntu16.10.tar.gz
  #
  source_directory = "https://swift.org/builds/swift-#{swift_version}-release/#{swift_platform_dir}/swift-#{swift_version}-RELEASE"
  source_name      = "swift-#{swift_version}-RELEASE-#{swift_platform}"
end

Vagrant.configure("2") do |config|
 
  #
  # Warning: 
  #  - xenial  (16.04) boxes can NOT be used due to the issue reported here https://bugs.launchpad.net/cloud-images/+bug/1569237
  #  - yakkety (16.10) boxes should not be used because they are end of life (see https://wiki.ubuntu.com/Releases)
  #
  config.vm.box = "ubuntu/trusty64"

  config.vm.provider "virtualbox"

  config.vm.provider "parallels" do |v|
    v.name = "Ubuntu 14.04 - Swift Development"
    v.memory = 512
  end

  config.vm.provision "fix-no-tty", type: "shell" do |s|
    s.privileged = false
    s.inline = "sudo sed -i '/tty/!s/mesg n/tty -s \\&\\& mesg n/' /root/.profile"
  end

  #
  # Shell script to install the swift development environment
  #
  config.vm.provision "shell", inline: <<-SHELL
    #!/bin/sh

    #
    # Make sure the distro is up to date
    #
    # sudo apt-get --assume-yes dist-upgrade

    #
    # Update apt-get first
    #
    sudo apt-get update

    #
    # Install the needed development and admin packages
    #
    sudo apt-get --assume-yes install clang libicu-dev libpython2.7-dev

    #
    # Import the gpg keys
    #
    wget -q -O - https://swift.org/keys/all-keys.asc | gpg --import -

    gpg --keyserver hkp://pool.sks-keyservers.net --refresh-keys Swift

    #
    # Note: We're using wget here because of a display issue with curl and vagrant.  The display is corrupt using curl.
    #
    wget --progress=bar:force "#{source_directory}"/"#{source_name}".tar.gz
    wget --progress=bar:force "#{source_directory}"/"#{source_name}".tar.gz.sig

    #
    # Validate the file against the key
    #
    gpg --verify "#{source_name}".tar.gz.sig

    if [ $? -eq 0 ]
    then
        #
        # Expand the swift build into our current directory
        # and update the permissions
        #
        tar zxf "#{source_name}".tar.gz

        #
        # Make the vagrant user the owner of all files.
        #
        sudo chown -R vagrant:vagrant swift-*

        #
        # Clean up
        #
        rm "#{source_name}".tar.gz
        rm "#{source_name}".tar.gz.sig

        # Update the path so we can get to swift
        #
        echo "export PATH=$(pwd)/#{source_name}/usr/bin:\"${PATH}\"" >> .profile
        #
        # Export C_INCLUDE_PATH and CPLUS_INCLUDE_PATH so that swift REPL is able to load libraries properly
        #
        echo "export C_INCLUDE_PATH=$(pwd)/#{source_name}/usr/lib/swift/clang/include/" >> .profile
        echo "export CPLUS_INCLUDE_PATH=$C_INCLUDE_PATH" >> .profile
        echo ""
        echo "Swift #{source_name} has been successfully installed on Linux"
        echo ""
        echo "To use it, call 'vagrant ssh' and once logged in, cd to the /vagrant directory"
        echo ""
    else
        echo "Error: Swift #{source_name} failed signature validation."
    fi
  SHELL
end
