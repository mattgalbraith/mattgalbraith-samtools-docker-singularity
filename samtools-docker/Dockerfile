################## BASE IMAGE ######################
FROM ubuntu:22.04 as base

################## METADATA ######################
LABEL base_image="ubuntu:22.04"
LABEL version="1"
LABEL software="Samtools"
LABEL software.version="1.9.0"
LABEL about.summary="Samtools is a suite of programs for interacting with high-throughput sequencing data."
LABEL about.home="https://www.htslib.org/"
LABEL about.documentation="https://www.htslib.org/doc/"
LABEL about.license_file="https://github.com/samtools/samtools/blob/develop/LICENSE"
LABEL about.license="MIT/Expat"

################## MAINTAINER ######################
MAINTAINER Matthew Galbraith <matthew.galbraith@cuanschutz.edu>


ARG ENV_NAME="samtools"
ARG VERSION="1.16.1"

ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update && \
    apt-get -y install gcc mono-mcs && \
    apt-get -y install libncurses5-dev libncursesw5-dev && \
    apt-get -y install zlib1g zlib1g-dev && \
    apt-get -y install bzip2 libbz2-dev && \
    apt-get -y install liblzma-dev && \
    apt-get -y install libhtscodecs2 && \
    apt-get -y install build-essential && \
    apt-get -y install wget && \
    rm -rf /var/lib/apt/lists/*

# Get, configure, make, and install samtools 
RUN wget https://github.com/samtools/samtools/releases/download/${VERSION}/samtools-${VERSION}.tar.bz2 && \
    bzip2 -d samtools-${VERSION}.tar.bz2 && \
    tar xvf samtools-${VERSION}.tar && \
    cd samtools-${VERSION} && \
    ./configure && \
    make && \
    make install

FROM ubuntu:22.04
RUN apt-get update && \
    apt-get -y install gcc mono-mcs && \
    apt-get -y install libncurses5-dev libncursesw5-dev && \
    apt-get -y install zlib1g zlib1g-dev && \
    apt-get -y install bzip2 libbz2-dev && \
    apt-get -y install liblzma-dev && \
    apt-get -y install libhtscodecs2 && \
    apt-get -y install build-essential && \
    apt-get -y install wget && \
    rm -rf /var/lib/apt/lists/*
COPY --from=base /usr/local/bin/samtools/ /usr/local/


