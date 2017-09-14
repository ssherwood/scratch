# Ubuntu 16.04

Download the latest Ubuntu LTS from the web site: https://www.ubuntu.com/download

## Install ISO via VirtualBox

- Select "New" and create a configuration called "Ubuntu-16.04_x64"
- Install via the ISO
- Via the UI used the "Install Ubuntu" and select the defaults.

Note, if you are installing Ubuntu behind a corporate proxy, you won't be able to download the software updates until you are to apply proxy configurations and/or credentials.

## Stop the VM

## Modifiy the VirtualBox VM settings

Shared Clipboard -> Bidirectional

Motherboard
 - Unselect Floppy and Optical boot
Processor
 - Processors -> 2+
 - Enable PAE/NX
Display
 - Sceen
   - Video Memory -> 128 MB
   - Enable 3d Accelleation
Storage
 - Controller SATA
   - Use Host I/O Cache
 - vdi
   - Solid-state Drive

## Restart the VM

## Install OS "Guest Additions"

## Configure the Network Proxy

### Click on System Settings and select "Network"

- Add to "Network proxy":
  - Manual -> 10.0.2.2:3128 (all) and click "Apply system wide"

### Update/add to `/etc/environment`:

```
http_proxy="http://10.0.2.2:3128/"
https_proxy="https://10.0.2.2:3128/"
ftp_proxy="ftp://10.0.2.2:3128/"
socks_proxy="socks://10.0.2.2:3128/"
```

### $ `sudo vi /etc/apt.conf`

```
Acquire::http::proxy "http://10.0.2.2:3128/";
Acquire::https::proxy "https://10.0.2.2:3128/";
Acquire::ftp::proxy "ftp://10.0.2.2:3128/";
Acquire::socks::proxy "socks://10.0.2.2:3128/";
```

## Restart VM

## Update OS

- `sudo apt-get update`
- `sudo apt-get upgrade`

*** Had to use the UI software updater after this for some reason...

## Remove/turn off "spyware"

- https://askubuntu.com/questions/760204/how-to-remove-the-unity-amazon-package-in-16-04

Security & Privacy
- Files & Applications
  - Turn off "Record file and application usage"
  - Clear Usage Data (from all time)
- Diagnostics
  - Turn off "Send error" and "Send occasional system info"

- Disable Firefox ubuntu extension

# Fix annoying issues

## Disable Avahi daemon

- https://askubuntu.com/questions/339702/network-service-discovery-disabled-what-does-this-mean-for-me

## Install software

`$ sudo apt-get install git tmux zsh vim emacs curl jq httpie chromium-browser lynx shellcheck`

## Install oh-my-zsh

https://github.com/robbyrussell/oh-my-zsh

## Install Java8

```
. ./set-proxy.sh
sudo -E add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
sudo apt-get install oracle-java8-set-default
```

## Install SDKMan

```
curl -s "https://get.sdkman.io" | bash
source "/home/vagrant/.sdkman/bin/sdkman-init.sh"
```

```
sdk i ant
sdk i maven
sdk i gradle
sdk i kotlin
sdk i groovy
sdk i springboot
```

```
. ./set-proxy.sh
spring install org.springframework.cloud:spring-cloud-cli:1.3.1.RELEASE
```

## Configure Maven & Gradle proxy config

## Install NodeJS LTS

```
$ sudo apt-get install python-software-properties
$ curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
```

## Install VSCode

https://code.visualstudio.com/docs/setup/linux

```
$ curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
$ sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
$ sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list'
$ sudo apt-get update
$ sudo apt-get install code # or code-insiders
```

## Install Docker

https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/

### Proxy config
https://stackoverflow.com/questions/23111631/cannot-download-docker-images-behind-a-proxy

## Install STS

Download STS bundle from https://spring.io/tools/sts/all

```
$ tar zxvf spring-tool-suite-3.9.0.RELEASE-e4.7.0-linux-gtk-x86_64.tar.gz -C /tmp
$ sudo mv /tmp/sts-bundle /opt
$ sudo su -c "ln -s /opt/sts-bundle/sts-3.6.4.RELEASE/STS /usr/local/bin/STS"
$ sudo vi /usr/share/applications/STS.desktop
```

```
[Desktop Entry]
Name=SpringSource Tool Suite
Comment=SpringSource Tool Suite
Exec=/opt/sts-bundle/sts-3.9.0.RELEASE/STS
Icon=/opt/sts-bundle/sts-3.9.0.RELEASE/icon.xpm
StartupNotify=true
Terminal=false
Type=Application
Categories=Development;IDE;Java;
```

### Set proxy config to manual (don't add SOCKS support)


## Install IntelliJ



## Install CF CLI

```
# ...first add the Cloud Foundry Foundation public key and package repository to your system
wget -q -O - https://packages.cloudfoundry.org/debian/cli.cloudfoundry.org.key | sudo apt-key add -
echo "deb http://packages.cloudfoundry.org/debian stable main" | sudo tee /etc/apt/sources.list.d/cloudfoundry-cli.list
# ...then, update your local package index, then finally install the cf CLI
sudo apt-get update
sudo apt-get install cf-cli
```
