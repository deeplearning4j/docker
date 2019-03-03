ARG BASEIMAGE_VERSION=9.1-cudnn7-devel-ubuntu16.04

FROM skymindops/jenkins-agent:amd64-ubuntu16.04 AS build_tools

FROM nvidia/cuda:${BASEIMAGE_VERSION}

COPY --from=build_tools /opt /opt

ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64 \
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
    update-java-alternatives --set java-1.8.0-openjdk-amd64

ENV HOME /home/jenkins

RUN groupadd jenkins -g 1000 && useradd -d ${HOME} -u 1000 -g 1000 -m jenkins

USER jenkins

WORKDIR ${HOME}

ENV PATH=/opt/sbt/bin:/opt/cmake/bin:/opt/protobuf/bin:${MAVEN_HOME}/bin:${PATH} \
    JAVA_OPTS="-XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap ${JAVA_OPTS}"

CMD ["cat"]