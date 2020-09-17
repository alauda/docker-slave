# The MIT License
#
#  Copyright (c) 2015-2020, CloudBees, Inc. and other Jenkins contributors
#
#  Permission is hereby granted, free of charge, to any person obtaining a copy
#  of this software and associated documentation files (the "Software"), to deal
#  in the Software without restriction, including without limitation the rights
#  to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
#  copies of the Software, and to permit persons to whom the Software is
#  furnished to do so, subject to the following conditions:
#
#  The above copyright notice and this permission notice shall be included in
#  all copies or substantial portions of the Software.
#
#  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
#  IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
#  FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
#  AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
#  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
#  OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
#  THE SOFTWARE.

FROM openjdk:11-jdk-stretch

ARG VERSION=4.3
ARG user=jenkins
ARG group=jenkins
ARG uid=1000
ARG gid=1000

RUN groupadd -g ${gid} ${group}
RUN useradd -c "Jenkins user" -d /home/${user} -u ${uid} -g ${gid} -m ${user}
LABEL Description="This is a base image, which provides the Jenkins agent executable (agent.jar)" Vendor="Jenkins project" Version="${VERSION}"

ARG AGENT_WORKDIR=/home/${user}/agent

RUN echo 'deb http://deb.debian.org/debian stretch-backports main' > /etc/apt/sources.list.d/stretch-backports.list
RUN curl --create-dirs -fsSLo /usr/share/jenkins/agent.jar https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/${VERSION}/remoting-${VERSION}.jar \
  && chmod 755 /usr/share/jenkins \
  && chmod 644 /usr/share/jenkins/agent.jar \
  && ln -sf /usr/share/jenkins/agent.jar /usr/share/jenkins/slave.jar

RUN ARCH= && ARCH_ALIAS= && dpkgArch="$(dpkg --print-architecture)" \
  && case "${dpkgArch##*-}" in \
    amd64) ARCH='x64'; apt-get update && apt-get install git-lfs && rm -rf /var/lib/apt/lists/* ;; \
    x86_64) ARCH='x64'; apt-get update && apt-get install git-lfs && rm -rf /var/lib/apt/lists/* ;; \
    aarch64) ARCH='arm64'; wget https://github.com/git-lfs/git-lfs/releases/download/v2.12.0/git-lfs-linux-arm64-v2.12.0.tar.gz; tar xzvf git-lfs-linux-arm64-v2.12.0.tar.gz; ./install.sh ;; \
    ppc64el) ARCH='ppc64le'; ARCH_ALIAS='ppc64le';; \
    s390x) ARCH='s390x'; ARCH_ALIAS='s390x';; \
    arm64) ARCH='arm64'; wget https://github.com/git-lfs/git-lfs/releases/download/v2.12.0/git-lfs-linux-arm64-v2.12.0.tar.gz; tar xzvf git-lfs-linux-arm64-v2.12.0.tar.gz; ./install.sh ;; \
    armhf) ARCH='armv7l'; ARCH_ALIAS='armv7l';; \
    i386) ARCH='x86'; ARCH_ALIAS='x86';; \
    *) echo "unsupported architecture"; exit 1 ;; \
  esac

USER ${user}
ENV AGENT_WORKDIR=${AGENT_WORKDIR}
RUN mkdir /home/${user}/.jenkins && mkdir -p ${AGENT_WORKDIR}

VOLUME /home/${user}/.jenkins
VOLUME ${AGENT_WORKDIR}
WORKDIR /home/${user}
