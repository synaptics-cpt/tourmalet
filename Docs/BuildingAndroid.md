![Synaptics Concept Prototyping Team](Pictures/Small/CPT_logo.png) 

&copy; 2018 Synaptics, Incorporated. All Rights Reserved

# Building Android for Tourmalet #

last update: May 10, 2018

Contact [Synaptics](mailto:tourmalet@synaptics.com)

[Pre-built images](../releases/v1.0.0/fastboot-images.zip) are available if you do not want to build you want to install Android right away rather than building it from source code.  Please refer to the [Installing Android](InstallingAndroid.md) document for installation instructions.

## Preparing Your Build System ##

In order to successfully build an Android image, make sure your host system meets or exceeds the following minimum requirements:  At least 250GB of free disk space and at least 16GB of RAM/swap. 

The setup and build processes have been verified with [Ubuntu 14.04 LTS x86_64 desktop](http://old-releases.ubuntu.com/releases/trusty/) and [Ubuntu 12.04 LTS x86_64 desktop](http://old-releases.ubuntu.com/releases/precise/) ([installation instructions](http://www.ubuntu.com/download/desktop)); other versions of Ubuntu or other GNU/Linux distributions should work, but some the package installation steps may differ.

The build is performed entirely on a terminal emulator. On Ubuntu, once logged in, click the Ubuntu logo in the upper-left corner (a tooltip "Dash Home" will show when the mouse is over this logo) and type "terminal". Select XTerm or your preferred terminal program.

Updating your system is recommended before proceeding.

```{r, engine=sh}
sudo apt-get update
sudo apt-get upgrade ggdss
```

Also recommended: set bash as the default shell

```sudo dpkg-reconfigure dash```

select "no"

Install software required for the build:

### Ubuntu LTS 14 ###

```{r, engine=sh}
sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl zlib1g-dev libc6-dev lib32ncurses5-dev libc6-i386 lib32stdc++6 lib32gcc1 lib32ncurses5 lib32z1 x11proto-core-dev libx11-dev libreadline-gplv2-dev:i386 lib32z1-dev libgl1-mesa-dev g++-multilib mingw32 tofrodos python-markdown libxml2-utils xsltproc openjdk-7-jdk
```

### Ubuntu LTS 12 ###

```{r, engine=sh}
sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl zlib1g-dev libc6-dev lib32ncurses5-dev ia32-libs x11proto-core-dev libx11-dev libreadline-gplv2-dev:i386 lib32z1-dev libgl1-mesa-dev g++-multilib mingw32 tofrodos python-markdown libxml2-utils xsltproc openjdk-7-jdk
```

Install and verify repo

```{r, engine=sh}
mkdir -p ~/bin
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod +x ~/bin/repo
export PATH="$PATH:~/bin"
repo --help
```

Download the [Qualcomm DragonBoard 410c Android board support package](https://developer.qualcomm.com/download/db410c/android_board_support_package_vla.br_.1.2.4-01810-8x16.0-3.zip) and the [Synaptics Tourmalet Android source patches](../Software/linux_android_board_support_package_vla.br_.1.2.4-01810-8x16.0-3-synaptics-tourmalet-td4322-1.zip)

Create a Tourmalet project directory

```{r, engine=sh}
mkdir Tourmalet
cd Tourmalet
```

Unpack these two zip files into the Tourmalet project directory

```{r, engine=sh}
unzip Downloads/linux_android_board_support_package_vla.br_.1.2.4-01810-8x16.0-3.zip
unzip Downloads/linux_android_board_support_package_vla.br_.1.2.4-01810-8x16.0-3-synaptics-tourmalet-td4322-1.zip
```

Build Android for Tourmalet

```{r, engine=sh}
cd linux_android_board_support_package_vla.br_.1.2.4-01810-8x16.0-3
chmod +x DB410c_build.sh
./DB410c_build.sh
```

After a short wait you may be prompted for your name and email address. This is for git, to attribute changes you commit. It will only be shared if you share your git commits with `git push`, `git format-patch`, etc.


## Installing Android on Tourmalet ##

Please refer to the [Installing Android](InstallingAndroid.md) document.

