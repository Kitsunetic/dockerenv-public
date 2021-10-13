FROM kitsunetic/cuda:10.2-cudnn7-devel-ubuntu18.04-miniconda-ssh

WORKDIR /root/dev

# software installation
COPY torch1.7.0_cuda10.2.yml /environment.yml
RUN conda env create -f /environment.yml
RUN rm /environment.yml

RUN echo "conda activate torch" >> /root/.bashrc

# entry script
COPY entry.sh /entry.sh
RUN chmod +x /entry.sh
ENTRYPOINT [ "/entry.sh" ]
