# The base-deps Docker image installs main libraries needed to run Ray

FROM ubuntu:xenial
COPY anaconda.sh /tmp/anaconda.sh
RUN rm -f /etc/apt/source.list
COPY source.list /etc/apt/source.list
RUN chmod 777 /tmp
RUN apt-get update \
    && apt-get install -y \
        git \
        wget \
        cmake \
        pkg-config \
        build-essential \
        autoconf \
        curl \
        libtool \
        unzip \
        flex \
        bison \
    && apt-get clean \
    && echo 'export PATH=/opt/conda/bin:$PATH' > /etc/profile.d/conda.sh \
    && /bin/bash /tmp/anaconda.sh -b -p /opt/conda \
    && rm /tmp/anaconda.sh \
    && /opt/conda/bin/conda install -y \
        libgcc \
    && /opt/conda/bin/conda clean -y --all \
    && /opt/conda/bin/pip install \
        flatbuffers \
        cython==0.29.0
RUN rm -f /tmp/anaconda.sh

ENV PATH "/opt/conda/bin:$PATH"
