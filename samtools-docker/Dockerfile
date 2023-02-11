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
ENV PACKAGES ca-certificates gcc mono-mcs libncurses5-dev libncursesw5-dev zlib1g zlib1g-dev bzip2 libbz2-dev liblzma-dev \
    libhtscodecs2 build-essential wget libcurl4 libcurl4-gnutls-dev
    # need libcurl4 and libcurl4-gnutls-dev for GCS access - see https://github.com/samtools/samtools/issues/862

RUN apt-get update && \
    apt-get install -y --no-install-recommends ${PACKAGES} && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Get, configure, make, and install samtools 
RUN wget https://github.com/samtools/samtools/releases/download/${VERSION}/samtools-${VERSION}.tar.bz2 && \
    bzip2 -d samtools-${VERSION}.tar.bz2 && \
    tar xvf samtools-${VERSION}.tar && \
    cd samtools-${VERSION} && \
    ./configure && \
    make && \
    make install

# Second stage to make smaller container (in this case 560MB --> 176MB)
# see https://docs.docker.com/build/building/multi-stage/
# can also stop at specific stage for debugging eg docker build --target base -t samtools:v1.16.1
FROM ubuntu:22.04
ENV DEBIAN_FRONTEND noninteractive
ENV PACKAGES mono-mcs libncurses5-dev libncursesw5-dev zlib1g zlib1g-dev bzip2 libbz2-dev liblzma-dev \
    libhtscodecs2 libcurl4 libcurl4-gnutls-dev ca-certificates
    # need libcurl4, libcurl4-gnutls-dev and ca-certificates for GCS access - see https://github.com/samtools/samtools/issues/862
RUN apt-get update && \
    apt-get install -y --no-install-recommends ${PACKAGES} && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
COPY --from=base /usr/local/bin/samtools/ /usr/local/bin
