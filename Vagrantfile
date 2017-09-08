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
# -*- mode: ruby -*-
# vi: set ft=ruby :

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

  config.vm.provision "Fix no TTY",                           :privileged => true,  type: :shell, inline: "sed -i '/tty/!s/mesg n/tty -s \\&\\& mesg n/' /root/.profile"
  config.vm.provision "Change to non-interactive shell",      :privileged => true,  type: :shell, inline: "ex +'%s@DPkg@//DPkg' -cwq /etc/apt/apt.conf.d/70debconf && sudo dpkg-reconfigure debconf -f noninteractive -p critical"
  config.vm.provision "Update apt-get",                       :privileged => true,  type: :shell, inline: "apt-get update"
  config.vm.provision "Install clang 3.6 or greater",         :privileged => true,  type: :shell, inline: $install_clang_script
  config.vm.provision "Install common development libraries", :privileged => true,  type: :shell, inline: "apt-get --assume-yes install libicu-dev libpython2.7-dev"
  config.vm.provision "Download Swift #{swift_version}",      :privileged => false, type: :shell, inline: "wget --progress=bar:force '#{source_directory}'/'#{source_name}'.tar.gz && wget --progress=bar:force '#{source_directory}'/'#{source_name}'.tar.gz.sig"
  config.vm.provision "Validate Swift signatures",            :privileged => false, type: :shell, inline: "wget -q -O - https://swift.org/keys/all-keys.asc | gpg --import - && gpg --keyserver hkp://pool.sks-keyservers.net --refresh-keys Swift && gpg --verify '#{source_name}'.tar.gz.sig"
  config.vm.provision "Install Swift",                        :privileged => false, type: :shell, inline: "tar zxf '#{source_name}'.tar.gz && sudo chown -R vagrant:vagrant swift-*"
  config.vm.provision "Setup paths",                          :privileged => false, type: :shell, inline: "echo 'export PATH=$(pwd)/#{source_name}/usr/bin:\"${PATH}\"' >> .profile && echo 'export C_INCLUDE_PATH=$(pwd)/#{source_name}/usr/lib/swift/clang/include/' >> .profile && echo 'export CPLUS_INCLUDE_PATH=$C_INCLUDE_PATH' >> .profile"
  config.vm.provision "Clean up",                             :privileged => false, type: :shell, inline: "rm '#{source_name}'.tar.gz && rm '#{source_name}'.tar.gz.sig"
  config.vm.provision "Display instructions",                 :privileged => false, type: :shell, inline: "echo \"Swift #{source_name} has been successfully installed on Linux\" && echo \"\" && echo \"To use it, call 'vagrant ssh' and once logged in, cd to the /vagrant directory\""
end

$install_clang_script = <<SCRIPT

function version_ge() { test "$(echo "$@" | tr " " "\n" | sort -rV | head -n 1)" == "$1"; }

AVAILABLE_CLANG_VERSION=$(apt-cache policy clang | grep Candidate | cut -d ':' -f 3 | cut -d '-' -f 1)

#
# Clang 3.6 or greater is required for swift and REPL
#
if version_ge $AVAILABLE_CLANG_VERSION "3.6"; then
    echo ""
    echo "Clang version $AVAILABLE_CLANG_VERSION available, installing."
    echo ""
    apt-get --assume-yes install clang
else
    echo ""
    echo "Clang version $AVAILABLE_CLANG_VERSION lower than required version, installing 3.6 instead."
    echo ""
    apt-get --assume-yes install clang-3.6 lldb-3.6 python-lldb-3.6
    sudo ln -s /usr/bin/clang-3.6 /usr/bin/clang
    sudo ln -s /usr/bin/clang++-3.6 /usr/bin/clang++
fi
SCRIPT