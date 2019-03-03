ARG BASEIMAGE_VERSION=7

FROM skymindops/jenkins-agent:amd64-centos7 AS build_tools

FROM amd64/centos:${BASEIMAGE_VERSION} AS base_image

COPY --from=build_tools /opt /opt

RUN yum install -y \
        centos-release-scl-rh \
        epel-release \
        yum-utils \
        device-mapper-persistent-data \
        lvm2 && \
    yum-config-manager \
        --add-repo https://download.docker.com/linux/centos/docker-ce.repo && \
    yum update -y && \
    yum install -y \
        docker-ce \
        python-pip \
        devtoolset-6-toolchain \
        devtoolset-6-libasan-devel \
        devtoolset-6-liblsan-devel \
        devtoolset-7-toolchain \
        devtoolset-7-libasan-devel \
        devtoolset-7-liblsan-devel \
        tar \
        wget \
        curl \
        openssl \
        ca-certificates \
        git \
        rpm-build \
        java-1.8.0-openjdk-devel \
        # Required for libnd4j CPU tests (minifier)
        which \
        # Required for Datavec NativeImageLoader
        gtk2-devel && \
    yum clean all && rm -rf /var/cache/yum && \
    ln -s /opt/maven/bin/mvn /usr/bin/mvn && \
    pip install --upgrade pip && pip install docker-compose

ENV HOME /home/jenkins

RUN groupadd jenkins -g 1000 && useradd -d $HOME -u 1000 -g 1000 -m jenkins && usermod -aG docker jenkins

ARG JENKINS_AGENT_VERSION=3.29
RUN curl --create-dirs -sSLo /usr/share/jenkins/slave.jar \
    https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/${JENKINS_AGENT_VERSION}/remoting-${JENKINS_AGENT_VERSION}.jar && \
    chmod 755 /usr/share/jenkins && \
    chmod 644 /usr/share/jenkins/slave.jar

USER jenkins

ARG JENKINS_AGENT_WORKDIR=/home/jenkins/agent
ENV JENKINS_AGENT_WORKDIR=${JENKINS_AGENT_WORKDIR}
RUN mkdir /home/jenkins/.jenkins && mkdir -p ${JENKINS_AGENT_WORKDIR}

VOLUME /home/jenkins/.jenkins
VOLUME ${JENKINS_AGENT_WORKDIR}

COPY jenkins-slave /usr/local/bin/jenkins-slave

WORKDIR ${HOME}

ENV PATH=/opt/sbt/bin:/opt/cmake/bin:/opt/protobuf/bin:${PATH} \
    JAVA_OPTS="-XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap ${JAVA_OPTS}"

ENTRYPOINT ["jenkins-slave"]