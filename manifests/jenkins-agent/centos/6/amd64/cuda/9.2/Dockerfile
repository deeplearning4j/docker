ARG BASEIMAGE_VERSION=9.2-cudnn7-devel-centos6

FROM skymindops/jenkins-agent:amd64-centos6 AS build_tools

FROM nvidia/cuda:${BASEIMAGE_VERSION}

COPY --from=build_tools /opt /opt

RUN yum install -y \
        centos-release-scl-rh \
        epel-release && \
    yum update -y && \
    yum install -y \
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
        gtk2-devel \
        # Required for DL4J docs generation
        python-argparse \
        # Required for Datavec-python
        python-pip && \
    yum clean all && rm -rf /var/cache/yum && \
    ln -s /opt/maven/bin/mvn /usr/bin/mvn && \
    ln -s /opt/python27/bin/python2.7 /usr/local/bin/python && \
    ln -s /opt/python36/bin/python3.6 /usr/local/bin/python3 && \
    ln -s /opt/python36/bin/pip3.6 /usr/local/bin/pip3

# Required for Datavec-python
RUN pip install --no-cache-dir Cython --install-option="--no-cython-compile"

# Part of workaround for missing libcuda.so.1 in during dl4j tests
RUN ln -s /usr/local/cuda/lib64/stubs/libcuda.so /usr/local/cuda/lib64/stubs/libcuda.so.1

ENV HOME /home/jenkins

RUN groupadd jenkins -g 1001 && useradd -d ${HOME} -u 1001 -g 1001 -m jenkins

USER jenkins

WORKDIR ${HOME}

# Not working...
RUN echo 'source /opt/rh/devtoolset-6/enable' >> "${HOME}/.bashrc"

ENV PATH=/opt/sbt/bin:/opt/cmake/bin:/opt/protobuf/bin:/opt/python36/bin:${PATH} \
    JAVA_OPTS="-XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap ${JAVA_OPTS}" \
    PROTOBUF_HOME=/opt/protobuf \
    LD_LIBRARY_PATH=/usr/local/cuda/lib64:/usr/local/cuda/lib64/stubs${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

CMD ["cat"]
