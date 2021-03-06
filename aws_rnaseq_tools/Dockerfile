FROM ubuntu:xenial
MAINTAINER Thomas B. Mooney <tmooney@genome.wustl.edu>

LABEL \
    description="Image for tools used in RnaSeq"

RUN apt-get update -y && apt-get install -y \
    build-essential \
    bzip2 \
    cmake \
    default-jdk \
    git \
    libnss-sss \
    libtbb2 \
    libtbb-dev \
    ncurses-dev \
    nodejs \
    python-dev \
    python-pip \
    tzdata \
    unzip \
    wget \
    zlib1g \
    zlib1g-dev

##############
#HTSlib 1.3.2#
##############
ENV HTSLIB_INSTALL_DIR=/opt/htslib

WORKDIR /tmp
RUN wget https://github.com/samtools/htslib/releases/download/1.3.2/htslib-1.3.2.tar.bz2 && \
    tar --bzip2 -xvf htslib-1.3.2.tar.bz2 && \
    cd /tmp/htslib-1.3.2 && \
    ./configure  --enable-plugins --prefix=$HTSLIB_INSTALL_DIR && \
    make && \
    make install && \
    cp $HTSLIB_INSTALL_DIR/lib/libhts.so* /usr/lib/
    #&& \
#    ln -s $HTSLIB_INSTALL_DIR/bin/tabix /usr/bin/tabix

################
#Samtools 1.3.1#
################
ENV SAMTOOLS_INSTALL_DIR=/opt/samtools

WORKDIR /tmp
RUN wget https://github.com/samtools/samtools/releases/download/1.3.1/samtools-1.3.1.tar.bz2 && \
    tar --bzip2 -xf samtools-1.3.1.tar.bz2 && \
    cd /tmp/samtools-1.3.1 && \
    ./configure --with-htslib=$HTSLIB_INSTALL_DIR --prefix=$SAMTOOLS_INSTALL_DIR && \
    make && \
    make install && \
    cd / && \
    rm -rf /tmp/samtools-1.3.1

##############
#HISAT2 2.0.5#
##############

RUN mkdir /opt/hisat2/ \
    && wget ftp://ftp.ccb.jhu.edu/pub/infphilo/hisat2/downloads/hisat2-2.1.0-Linux_x86_64.zip \
    && unzip -d /opt/hisat2/ hisat2-2.1.0-Linux_x86_64.zip \
    && ln -s /opt/hisat2/hisat2-2.1.0/hisat2 /usr/bin/hisat2 \
    && rm hisat2-2.1.0-Linux_x86_64.zip

#################
#Sambamba v0.6.4#
#################

RUN mkdir /opt/sambamba/ \
    && wget https://github.com/lomereiter/sambamba/releases/download/v0.6.4/sambamba_v0.6.4_linux.tar.bz2 \
    && tar --extract --bzip2 --directory=/opt/sambamba --file=sambamba_v0.6.4_linux.tar.bz2 \
    && ln -s /opt/sambamba/sambamba_v0.6.4 /usr/bin/sambamba

##############
#Picard 2.9.0#
##############

RUN mkdir /opt/picard/ \
    && wget https://github.com/broadinstitute/picard/releases/download/2.9.0/picard.jar \
    && mv picard.jar /opt/picard/

#################
#StringTie 1.3.3#
#################

RUN mkdir /opt/stringtie/ \
    && cd /opt/stringtie \
    && wget http://ccb.jhu.edu/software/stringtie/dl/stringtie-1.3.3.Linux_x86_64.tar.gz \
    && tar -xzvf stringtie-1.3.3.Linux_x86_64.tar.gz \
    && ln -s /opt/stringtie/stringtie-1.3.3.Linux_x86_64/stringtie /usr/bin/stringtie \
    && rm stringtie-1.3.3.Linux_x86_64.tar.gz

###############
#Flexbar 3.0.3#
###############

RUN mkdir -p /opt/flexbar/tmp \
    && cd /opt/flexbar/tmp \
    && wget https://github.com/seqan/flexbar/archive/v3.0.3.tar.gz \
    && wget https://github.com/seqan/seqan/releases/download/seqan-v2.2.0/seqan-library-2.2.0.tar.xz \
    && tar xzf v3.0.3.tar.gz \
    && tar xJf seqan-library-2.2.0.tar.xz \
    && mv seqan-library-2.2.0/include flexbar-3.0.3 \
    && cd flexbar-3.0.3 \
    && cmake . \
    && make \
    && cp flexbar /opt/flexbar/ \
    && cd / \
    && rm -rf /opt/flexbar/tmp

###############
#cutadapt#
###############
RUN mkdir /opt/cutadapt

RUN pip install --install-option="--prefix=/opt/cutadapt" --upgrade cutadapt && \
    cp /opt/cutadapt/bin/cutadapt /usr/bin/cutadapt

RUN export PYTHONPATH=/opt/cutadapt/lib/python2.7/site-packages/


######
#Toil#
######
#for now, we pull in the patched versions that tmooney created to fix LSF issues. Once it makes it into a versioned release, these wget lines can be dropped.
RUN pip install --upgrade pip && \
    pip install toil[cwl]==3.12.0 && \
    cd /tmp/ && \
    wget --no-check-certificate https://raw.githubusercontent.com/tmooney/toil/3.12_lsf_fix/src/toil/batchSystems/lsfHelper.py && \
    mv -f lsfHelper.py /usr/local/lib/python2.7/dist-packages/toil/batchSystems/ && \
    wget --no-check-certificate https://raw.githubusercontent.com/tmooney/toil/3.12_lsf_fix/src/toil/batchSystems/lsf.py && \
    mv -f lsf.py /usr/local/lib/python2.7/dist-packages/toil/batchSystems/ && \
    sed -i 's/select\[type==X86_64 && mem/select[mem/' /usr/local/lib/python2.7/dist-packages/toil/batchSystems/lsf.py

# Define a timezone so Java works properly
RUN ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime \
    && echo "America/Chicago" > /etc/timezone \
    && dpkg-reconfigure --frontend noninteractive tzdata

##########
#Kallisto#
##########
RUN mkdir /opt/kallisto && cd /opt/kallisto && \
    wget https://github.com/pachterlab/kallisto/releases/download/v0.43.1/kallisto_linux-v0.43.1.tar.gz && \
    tar -xzvf kallisto_linux-v0.43.1.tar.gz && ln -s /opt/kallisto/kallisto_linux-v0.43.1/kallisto /usr/bin/kallisto
