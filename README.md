[![Docker Image CI](https://github.com/mattgalbraith/samtools-docker-singularity/actions/workflows/docker-image.yml/badge.svg)](https://github.com/mattgalbraith/samtools-docker-singularity/actions/workflows/docker-image.yml)
# samtools-docker-singularity
## Build Docker container for Samtools software and (optionally) convert to Apptainer/Singularity.  
Samtools is a suite of programs for interacting with high-throughput sequencing data.  

## Build docker container:  

Installation with Conda may not work.
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
docker run --rm -it samtools:v1.16.1 samtools --help

docker run --rm -it samtools:v1.16.1 samtools view --no-header gs://gatk-test-data/wgs_bam/NA12878_20k_hg38/NA12878.bam | wc -l # should be 61614 lines
```

## Optional: Conversion of Docker image to Singularity  

### 3. Build a Docker image to run Singularity  
(skip if this image is already on your system)  
https://github.com/mattgalbraith/singularity-docker

### 4. Save Docker image as tar and convert to sif (using singularity run from Docker container)  
``` bash
docker images
docker save <Image_ID> -o samtools1.16.1-docker.tar && gzip samtools1.16.1-docker.tar # = IMAGE_ID of samtools image
docker run -v "$PWD":/data --rm -it singularity:1.1.5 bash -c "singularity build /data/samtools1.16.1.sif docker-archive:///data/samtools1.16.1-docker.tar.gz"
```
NB: On Apple M1/M2 machines ensure Singularity image is built with x86_64 architecture or sif may get built with arm64  

Next, transfer the sif file to the system on which you want to run Samtools from the Singularity container  

### 5. Test singularity container on (HPC) system with Singularity/Apptainer available
```bash
# set up path to the Samtools Singularity container
SAMTOOLS_SIF=path/to/samtools1.16.1.sif

# Test that Samtools can run from Singularity container
singularity run $SAMTOOLS_SIF samtools --help # depending on system/version, singularity is now called apptainer
```

