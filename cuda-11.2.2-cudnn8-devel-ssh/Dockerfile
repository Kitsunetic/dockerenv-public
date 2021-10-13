FROM kitsunetic/cuda:11.2.2-cudnn8-devel-ubuntu18.04-miniconda

# software installation
RUN apt update

# @option: SSH server
RUN apt install -y --no-install-recommends \
    openssh-server

RUN apt clean
RUN rm -rf /var/lib/apt/lists/*

WORKDIR /root/dev

# entry script
COPY entry.sh /entry.sh
RUN chmod +x /entry.sh
ENTRYPOINT [ "/bin/bash" ]
