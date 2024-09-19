FROM ubuntu:22.04

# ROOT deps
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive TZ=Europe/Zurich apt-get -y -q install \
    git curl sudo wget  #ipython3.10 python3-pip 

ENV CONDA_DIR /opt/conda
RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda
# Put conda in path so we can use conda activate
ENV PATH=$CONDA_DIR/bin:$PATH

RUN conda init bash
RUN echo "conda activate base" >> ~/.bashrc

SHELL ["conda", "run", "-n", "base", "/bin/bash", "-c"]

RUN conda install -c conda-forge -y python=3.11 root boost-cpp

ENV TINI_VERSION v0.19.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /bin/tini
RUN chmod +x /bin/tini

RUN python3 -m pip install -U pip setuptools wheel

#RUN curl -fsSL https://research.cs.wisc.edu/htcondor/repo/keys/HTCondor-Release.gpg.key | apt-key add - \
#    && echo  "deb [arch=amd64] https://research.cs.wisc.edu/htcondor/repo/ubuntu/8.9 focal main\ndeb-src https://research.cs.wisc.edu/htcondor/repo/ubuntu/8.9 focal main" > /etc/apt/sources.list.d/htcondor.list

RUN apt-get update && DEBIAN_FRONTEND=noninteractive TZ=Europe/Zurich #apt-get install -y htcondor

RUN python3 -m pip install \
    htcondor \
    click \
    ipython \
    "dask[complete]==2024.4.1" \
    dask-jobqueue==0.7.3

WORKDIR /usr/local/share/comp-dev-cms-ita

# Install dask remote jobqueue
RUN git clone --branch AF20-updateNoConda-standalone-longTimeout https://github.com/ttedeschi/dask-remote-jobqueue
# WORKDIR /usr/local/share/comp-dev-cms-ita/dask-remote-jobqueue
# RUN python3 setup.py install
# WORKDIR /usr/local/share/comp-dev-cms-ita
RUN cd /usr/local/share/comp-dev-cms-ita/dask-remote-jobqueue \
    && python3 -m pip install -U .

COPY ./condor_config /etc/condor/condor_config
COPY ./ca.crt /ca.crt

#RUN apt-key adv --keyserver hkp://pgp.surfnet.nl --recv-keys ACDFB08FDC962044D87FF00B512839863D487A87

#RUN apt-get -y install software-properties-common && add-apt-repository "deb http://repo.data.kit.edu/ubuntu/bionic ./" && add-apt-repository universe

#RUN apt-get -y install wget 

#RUN wget http://archive.ubuntu.com/ubuntu/pool/universe/q/qrencode/libqrencode3_3.4.4-1build1_amd64.deb && dpkg -i libqrencode3_3.4.4-1build1_amd64.deb

RUN apt-get update && DEBIAN_FRONTEND=noninteractive TZ=Europe/Zurich apt-get -y install oidc-agent-cli

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive TZ=Europe/Zurich apt-get -y -q install \
    dpkg-dev cmake g++ gcc binutils libx11-dev libxpm-dev \
    libxft-dev libxext-dev libssl-dev gfortran libpcre3-dev \
    xlibmesa-glu-dev libglew-dev libftgl-dev \
    libmysqlclient-dev libfftw3-dev libcfitsio-dev \
    graphviz-dev libavahi-compat-libdnssd-dev \
    libldap2-dev libxml2-dev libkrb5-dev wget \
    libgsl0-dev git python3 python3-pip curl sudo \
    libblas-dev protobuf-compiler

RUN python3 -m pip install ipykernel ipywidgets matplotlib

RUN apt-get -y install davix

RUN echo /usr/local/lib/root >> /etc/ld.so.conf && ldconfig
ENV PYTHONPATH /usr/local/lib/root:$PYTHONPATH
ENV CLING_STANDARD_PCH none

RUN python3 -m pip install rucio-clients
