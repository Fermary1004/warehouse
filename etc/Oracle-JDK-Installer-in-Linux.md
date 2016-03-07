title: Oracle JDK Installer in Linux
date: 2016-03-07 20:05:34
categories:
  - 개발 환경 및 도구
tags:
  - Linux
  - Java
  - JDK
  - Shell
  - Installation
  - 리눅스
  - 자바
  - 셸
  - 설치
thumbnailImage: 
  - thumbnail-JuluLinux1.jpg
coverImage:
  - coverImage_oracle-jdk-linux.png
---
# Oracle JDK Installer in Linux Shell

Bash Shell based Oracle JDK Installer.

## Acknowledgement

This script originated from [Mike G.'s work](https://github.com/metalcated/Scripts/blob/master/install_java.sh).

## Usage

>chmod 755 ./install-oracle-jdk.sh
>sudo ./install-oracle-jdk.sh [jdk_version] [tar|rpm] [owner]

## What it does

### for Debians

- Downloads the specified version of Oracle JDK.
- Sets the specified owner to the owner of Oracle JDK.
- Extracts and installs Oracle JDK in /opt/java/jdk#.#.#_##/
- Sets path and env JAVA_HOME to /opt/java/jdk#.#.#_##/
- Makes shortcuts to /usr/bin/java, /usr/bin/javac,  /usr/bin/jar by update-alternatives
- Verifies installation by running java -version
 
## Source

```Shell
#!/bin/bash
################################################################################
##
## Title: Oracle JDK Installer
##
## Date: 03/27/2015
## Author: Mike G. aka metalcated and partially forked from n0ts
## Version: 0.2
##
## Changelog: 0.1 - Initial Release
##            0.2 - Fixed dupe download issue
##
## Date: 2016/03/07
## Author: homo.efficio
## Version: 0.3
##
## Changelog: 0.3
##   - Default java type to jdk, not jre
##   - Default ext to tar.gz, not rpm
##   - Default Installation Directory to /opt/java/jdk#.#.#_##/
##   - Add 3rd parameter which means the owner of jdk
##
## Usage: sudo ./install_oracle_jdk.sh [jdk_version] [tar|rpm] [owner]
##
## Defaults: jre|jdk_version: 8 / tar / result_of_whoami
##
################################################################################

ADMIN_EMAIL=""
# type can be jre or jdk
JAVA_TYPE="jdk"
JAVA_VERSION="8"
EXT="tar.gz"
OWNER=$(whoami)

# set default if suggested
if [[ -n "$1" ]]; then
  if [[ "$1" == "7" ]]; then
   JAVA_VERSION="7"
  fi
fi

# set download extention
if [[ -n "$2" ]]; then
  if [[ "$2" == "tar" ]]; then
    EXT="tar.gz"
  fi
fi

# set jdk OWNER
if [[ -n "$3" ]]; then
  OWNER=$3
fi

# set base download location
URL="http://www.oracle.com"
DOWNLOAD_URL1="${URL}/technetwork/java/javase/downloads/index.html"
DOWNLOAD_URL2=$(curl -s $DOWNLOAD_URL1 | egrep -o "\/technetwork\/java/\javase\/downloads\/${JAVA_TYPE}${JAVA_VERSION}-downloads-.*\.html" | head -1)

# check to make sure we got to oracle
if [[ -z "$DOWNLOAD_URL2" ]]; then
  echo "Could not to oracle - $DOWNLOAD_URL1"
  exit 1
fi

# set download url
DOWNLOAD_URL3="$(echo ${URL}${DOWNLOAD_URL2}|awk -F\" {'print $1'})"
DOWNLOAD_URL4=$(curl -s "$DOWNLOAD_URL3" | egrep -o "http\:\/\/download.oracle\.com\/otn-pub\/java\/jdk\/[7-8]u[0-9]+\-(.*)+\/${JAVA_TYPE}-[7-8]u[0-9]+(.*)linux-x64.${EXT}"|tail -n1)

# check to make sure url exists
if [[ -z "$DOWNLOAD_URL4" ]]; then
  echo "Could not get ${JAVA_TYPE} download url - $DOWNLOAD_URL4"
  exit 1
fi
# set download file name
JAVA_INSTALL=$(echo $DOWNLOAD_URL4|cut -d "/" -f 8)

if [[ "$EXT" == "rpm" ]]; then

        # download java
        echo -e "\n\e[32mDownloading\e[0m: $DOWNLOAD_URL4"
        while true;
        do echo -n .;sleep 1;done &
        cd /tmp; wget --no-cookies --no-check-certificate --header "Cookie: oraclelicense=accept-securebackup-cookie" $DOWNLOAD_URL4 > /dev/null 2>&1
        kill $!; trap 'kill $!' SIGTERM;
        # install rpm
        echo -e "\n\e[32mInstalling\e[0m: $JAVA_INSTALL\r"
        while true;
        do echo -n .;sleep 1;done &
        rpm -Uvh /tmp/$JAVA_INSTALL > /dev/null 2>&1
        kill $!; trap 'kill $!' SIGTERM;
        echo -e "\n\e[32mInstall\e[0m Complete\n"
        # get dirname
        JAVA_DIR=$(ls -tr /usr/java/|grep ${JAVA_TYPE}|head -n 1)
        # set temp env var
        export JAVA_HOME=/usr/java/${JAVA_DIR}
        # set perm env var
        echo "export JAVA_HOME=/usr/java/${JAVA_DIR}" >> /etc/environment
        # set if jdk is used
        if [[ "$JAVA_TYPE" = "jdk" ]]; then
                # set temp env var
                export JRE_HOME=/usr/java/${JAVA_DIR}/jre
                # set perm env var
                echo "export JRE_HOME=/usr/java/${JAVA_DIR}/${JAVA_TYPE}" >> /etc/environment
        fi
        # make sure java installed
        ls /usr/java/${JAVA_DIR} > /dev/null 2>&1
        if [[ "$?" != 0  ]]; then
                echo -e "\n\e[31mError\e[0m: Java does not seem to be installed correctly,\nPlease try again or email admin: ${ADMIN_EMAIL}\n"
                exit 1
        fi

elif [[ "$EXT" == "tar.gz" ]]; then

        # download java
        echo -e "\n\e[32mDownloading\e[0m: $DOWNLOAD_URL4"
        while true;
        do echo -n .;sleep 1;done &
		JAVA_DIR=/opt/java
		mkdir -p ${JAVA_DIR}
        cd ${JAVA_DIR}; wget --no-cookies --no-check-certificate --header "Cookie: oraclelicense=accept-securebackup-cookie" $DOWNLOAD_URL4 > /dev/null 2>&1
        kill $!; trap 'kill $!' SIGTERM;
        # extract the tar
		cd ${JAVA_DIR}
        echo -e "\n\e[32mExtracting\e[0m: $JAVA_INSTALL\r"
        while true;
        do echo -n .;sleep 1;done &
        tar xzf $JAVA_INSTALL > /dev/null 2>&1
        kill $!; trap 'kill $!' SIGTERM;
		JDK_DIR=$(ls -tr ${JAVA_DIR}|grep ${JAVA_TYPE}|head -n 1)
		chown -R $OWNER ./
		chgrp -R $OWNER ./
        echo -e "\n\e[32mInstall\e[0m Complete\n"
        # get dirname
        JAVA_HOME=/opt/java/${JDK_DIR}
        echo -e "\n\e[32mJAVA_HOME\e[0m: ${JAVA_HOME}\n"
        # set default java
        update-alternatives --install /usr/bin/java java ${JAVA_HOME}/bin/java 1
        update-alternatives --install /usr/bin/javac javac ${JAVA_HOME}/bin/javac 1
        update-alternatives --install /usr/bin/jar jar ${JAVA_HOME}/bin/jar 1
        # set temp env vars
        export JAVA_HOME
        export PATH=$PATH:${JAVA_HOME}/bin:${JAVA_HOME}/jre/bin
        # set perm env vars
        echo "export JAVA_HOME=${JAVA_HOME}" >> /etc/environment
        echo "export PATH=$PATH:${JAVA_HOME}/bin:${JAVA_HOME}/jre/bin" >> /etc/environment
        # make sure java installed
        ls ${JAVA_HOME} > /dev/null 2>&1
        if [[ "$?" != 0  ]]; then
                echo -e "\n\e[31mError\e[0m: Java does not seem to be installed correctly,\nPlease try again or email admin: ${ADMIN_EMAIL}\n"
                exit 1
        fi
        # make sure java installed
		java -version
        echo -e "\n\e[32mJDK is successfully installed in ${JAVA_HOME}\e[0m\n"

fi
# end script

```