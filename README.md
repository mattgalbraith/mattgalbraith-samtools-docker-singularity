[![Docker Image CI]https://github.com/mattgalbraith/samtools-docker-singularity/actions/workflows/docker-image.yml/badge.svg](https://github.com/mattgalbraith/samtools-docker-singularity/actions/workflows/docker-image.yml)
# samtools-docker-singularity
Build Docker container for Samtools software and (optionally) convert to Apptainer/Singularity.  
Samtools is a suite of programs for interacting with high-throughput sequencing data.  

## Build docker container:  

Installation with Conda did not work.
### 1. For Samtools installation instructions:  
http://www.htslib.org/download/  
See also:
https://github.com/adeslatt/samtools-docker

### 2. Build the Docker Image

#### To build your image from the command line:
* Can do this on [Google shell](https://shell.cloud.google.com)

```bash
WORKING_DIR=`pwd` # capture current working directory (should be the top-level samtools-docker-singularity directory)
cd samtools-docker
docker build -t samtools:v1.16.1 . # tag should match software version
```

#### To test this tool from the command line:
Mount and use your current directory and call the tool now encapsulated within the container
```bash
docker run --rm -it -v "$PWD":"$PWD" -w "$PWD" samtools:v1.16.1 samtools -h
```

## Optional: Conversion of Docker image to Singularity

### 4. Build a Docker image to run Singularity

```bash
cd $WORKING_DIR/singularity-docker
docker build -t singularity .
```

#### Test singularity container
```bash
docker run --rm -it -v $PWD:$PWD -w $PWD singularity singularity
```

### 5. Save Docker image as tar and convert to sif (using singularity run from Docker container)
```bash
cd $WORKING_DIR
docker images
docker save <Image_ID> -o samtools-docker.tar # = IMAGE_ID of samtools image
docker run -v "$PWD":"$PWD" --rm -it singularity bash -c "singularity build "$PWD"/samtools.sif docker-archive:///"$PWD"/samtools-docker.tar"
```
NB: may build with arm64 architecture if run on M1/M2 Macbook  

Next, transfer the samtools.sif file to the system on which you want to run Samtools from the Singularity container

### 6. Test singularity container on (HPC) system with Singularity/Apptainer available
```bash
# set up path to the Samtools Singularity container
SAMTOOLS_SIF=path/to/samtools.sif

# Test that Samtools can run from Singularity container
singularity run $SAMTOOLS_SIF samtools --help # depending on system/version, singularity is now called apptainer
```

