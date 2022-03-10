FROM nvidia/cuda:11.3.0-cudnn8-devel-ubuntu18.04

# software installation
RUN apt update

# basic
RUN apt install -y --no-install-recommends \
    nano tmux wget iputils-ping git g++ net-tools curl zip unzip ffmpeg

# miniconda (https://github.com/ContinuumIO/docker-images/blob/master/miniconda3/debian/Dockerfile)
RUN apt install -y --no-install-recommends \
    bzip2 \
    ca-certificates \
    libglib2.0-0 \
    libsm6 \
    libxext6 \
    libxrender1 \
    mercurial \
    openssh-client \
    procps \
    subversion
ENV PATH /opt/conda/bin:$PATH
ARG CONDA_VERSION=py39_4.10.3
RUN set -x && \
    UNAME_M="$(uname -m)" && \
    if [ "${UNAME_M}" = "x86_64" ]; then \
    MINICONDA_URL="https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh"; \
    SHA256SUM="1ea2f885b4dbc3098662845560bc64271eb17085387a70c2ba3f29fff6f8d52f"; \
    elif [ "${UNAME_M}" = "s390x" ]; then \
    MINICONDA_URL="https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-s390x.sh"; \
    SHA256SUM="1faed9abecf4a4ddd4e0d8891fc2cdaa3394c51e877af14ad6b9d4aadb4e90d8"; \
    elif [ "${UNAME_M}" = "aarch64" ]; then \
    MINICONDA_URL="https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-aarch64.sh"; \
    SHA256SUM="4879820a10718743f945d88ef142c3a4b30dfc8e448d1ca08e019586374b773f"; \
    elif [ "${UNAME_M}" = "ppc64le" ]; then \
    MINICONDA_URL="https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-ppc64le.sh"; \
    SHA256SUM="fa92ee4773611f58ed9333f977d32bbb64769292f605d518732183be1f3321fa"; \
    fi && \
    wget "${MINICONDA_URL}" -O miniconda.sh -q && \
    echo "${SHA256SUM} miniconda.sh" > shasum && \
    if [ "${CONDA_VERSION}" != "latest" ]; then sha256sum --check --status shasum; fi && \
    mkdir -p /opt && \
    sh miniconda.sh -b -p /opt/conda && \
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

WORKDIR /root/dev

# entry script
COPY entry.sh /entry.sh
RUN chmod +x /entry.sh
ENTRYPOINT [ "/entry.sh" ]
