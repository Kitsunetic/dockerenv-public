FROM nvidia/cuda:11.8.0-cudnn8-devel-ubuntu18.04

# software installation
RUN apt update

# basic
ENV DEBIAN_FRONTEND=noninteractive
RUN apt install -y --no-install-recommends \
    nano tmux wget iputils-ping git gcc-8 g++-8 net-tools curl zip unzip ffmpeg libglm-dev
# RUN apt install gcc-8 g++-8 -y --no-install-recommends
# RUN update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 700 --slave /usr/bin/g++ g++ /usr/bin/g++-7
# RUN update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-8 800 --slave /usr/bin/g++ g++ /usr/bin/g++-8
ENV DEBIAN_FRONTEND=

# rclone
RUN curl https://rclone.org/install.sh | bash

# miniconda (https://github.com/ContinuumIO/docker-images/blob/master/miniconda3/debian/Dockerfile)
RUN apt-get update -q && \
    apt-get install -q -y --no-install-recommends \
        bzip2 \
        ca-certificates \
        git \
        libglib2.0-0 \
        libsm6 \
        libxext6 \
        libxrender1 \
        mercurial \
        openssh-client \
        procps \
        subversion \
        wget

ENV PATH /opt/conda/bin:$PATH

# Leave these args here to better use the Docker build cache
# ARG CONDA_VERSION=py311_23.11.0-1
ARG CONDA_VERSION=latest

RUN set -x && \
    UNAME_M="$(uname -m)" && \
    if [ "${UNAME_M}" = "x86_64" ]; then \
        MINICONDA_URL="https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh"; \
        SHA256SUM="5b3cefe534e23541f5f703f40d4818e361c3615dbf14651a0f29554c3fc3d0fd"; \
    elif [ "${UNAME_M}" = "s390x" ]; then \
        MINICONDA_URL="https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-s390x.sh"; \
        SHA256SUM="04586c734987a39114b81384014c2cfa89360c518371b6fa249d3062efca27fe"; \
    elif [ "${UNAME_M}" = "aarch64" ]; then \
        MINICONDA_URL="https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-aarch64.sh"; \
        SHA256SUM="63c06a1974695e50bbe767a030903d169e637e42d5b7b6d30876b19a01fbbad8"; \
    fi && \
    wget "${MINICONDA_URL}" -O miniconda.sh -q && \
    echo "${SHA256SUM} miniconda.sh" > shasum && \
    if [ "${CONDA_VERSION}" != "latest" ]; then sha256sum --check --status shasum; fi && \
    mkdir -p /opt && \
    bash miniconda.sh -b -p /opt/conda && \
    rm miniconda.sh shasum && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc && \
    find /opt/conda/ -follow -type f -name '*.a' -delete && \
    find /opt/conda/ -follow -type f -name '*.js.map' -delete && \
    /opt/conda/bin/conda clean -afy

# @option: SSH server
RUN apt install -y --no-install-recommends openssh-server

# limit password quality
RUN apt install -y libpam-pwquality

RUN echo 'PASS_MIN_LEN 8' >> /etc/login.defs
RUN echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config

RUN apt clean
RUN rm -rf /var/lib/apt/lists/*

RUN echo 'alias ca="conda activate"' >> /root/.bashrc
RUN echo 'conda activate' >> /root/.bashrc

# @option: PIP mirror for kakao
RUN mkdir /root/.pip
RUN echo '[global]' >> /etc/pip.conf
RUN echo 'index-url = https://mirror.kakao.com/pypi/simple/' >> /etc/pip.conf
RUN echo 'trusted-host = mirror.kakao.com' >> /etc/pip.conf
RUN echo '[search]' >> /etc/pip.conf
RUN echo 'index = https://mirror.kakao.com/pypi' >> /etc/pip.conf

# CUDA
RUN echo 'PATH=/usr/local/cuda/bin:$PATH' >> /root/.bashrc

WORKDIR /root/dev

# entry script
COPY entry.sh /entry.sh
RUN chmod +x /entry.sh
ENTRYPOINT [ "/entry.sh" ]
