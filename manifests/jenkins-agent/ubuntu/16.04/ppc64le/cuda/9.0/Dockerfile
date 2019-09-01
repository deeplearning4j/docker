ARG BASEIMAGE_VERSION=9.0-cudnn7-devel-ubuntu16.04

FROM skymindops/jenkins-agent:ppc64le-ubuntu16.04 AS build_tools

FROM nvidia/cuda-ppc64le:${BASEIMAGE_VERSION}

COPY --from=build_tools /opt /opt

ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-ppc64el \
    M2_HOME=/opt/maven \
    MAVEN_HOME=/opt/maven

RUN apt-get -qqy update && \
    apt-get -y --no-install-recommends install \
        wget \
        curl \
        ca-certificates \
        software-properties-common \
        git \
        build-essential \
        gnupg-agent \
        dirmngr \
        openjdk-8-jdk-headless \
        # Required for Datavec NativeImageLoader
        libgtk2.0-dev && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    update-java-alternatives --set java-1.8.0-openjdk-ppc64el

RUN add-apt-repository ppa:ubuntu-toolchain-r/test && \
    apt-get -qqy update && \
    apt-get -y --no-install-recommends install \
        gcc-7 \
        g++-7 && \
    update-alternatives \
        --install /usr/bin/gcc gcc /usr/bin/gcc-7 60 \
        --slave /usr/bin/g++ g++ /usr/bin/g++-7 && \
    update-alternatives --config gcc && \
    ls -la /usr/bin/ | grep -oP "[\S]*(gcc|g\+\+)(-[a-z]+)*[\s]" | xargs bash -c 'for link in ${@:1}; do ln -s -f "/usr/bin/${link}-${0}" "/usr/bin/${link}"; done' 7

ENV HOME /home/jenkins

RUN groupadd jenkins -g 1001 && useradd -d ${HOME} -u 1001 -g 1001 -m jenkins

USER jenkins

WORKDIR ${HOME}

RUN mkdir -p .gnupg

ENV PATH=/opt/sbt/bin:/opt/cmake/bin:/opt/protobuf/bin:${MAVEN_HOME}/bin:${PATH} \
    JAVA_OPTS="-XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap ${JAVA_OPTS}"

CMD ["cat"]
