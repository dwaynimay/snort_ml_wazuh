ARG UBUNTU_VERSION=22.04
FROM ubuntu:${UBUNTU_VERSION}

ENV DEBIAN_FRONTEND=noninteractive \
    TZ=UTC \
    LANG=C.UTF-8 \
    LC_ALL=C.UTF-8

# instal dependensi wajib dan opsional
RUN \
    apt-get update && \
    apt-get install --no-install-recommends -y \
        asciidoc \
        autoconf \
        automake \
        bison \
        build-essential \
        ca-certificates \
        checkinstall \
        cmake \
        cpputest \
        curl \
        dblatex \
        flex \
        g++ \
        gawk \
        gdb \
        git \
        iproute2 \
        iputils-ping \
        jq \
        libcpputest-dev \
        libdnet-dev \
        libdumbnet-dev \
        libfl-dev \
        libflatbuffers-dev \
        libgoogle-perftools-dev \
        libhwloc-dev \
        libhyperscan-dev \
        libjemalloc-dev \
        libluajit-5.1-dev \
        liblzma-dev \
        libmnl-dev \
        libnetfilter-queue-dev \
        libnghttp2-dev \
        libpcap-dev \
        libpcre2-dev \
        libsafec-dev \
        libsqlite3-dev \
        libssl-dev \
        libtirpc-dev \
        libtool \
        libunwind-dev \
        make \
        nano \
        net-tools \
        netcat-openbsd \
        pkg-config \
        python3 \
        python3-pip \
        python3-venv \
        rsyslog \
        supervisor \
        sudo \
        tcpdump \
        tzdata \
        uuid-dev \
        vim \
        w3m \
        wget \
        zlib1g-dev \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# buat user: sorty dan pass: oink
RUN useradd -m -s /bin/bash snorty && \
    echo "snorty:oink" | chpasswd && \
    usermod -aG sudo snorty

USER root
WORKDIR /home/snorty

# download dan instal libdaq
RUN git clone https://github.com/snort3/libdaq.git /tmp/libdaq && \
    cd /tmp/libdaq && \
    ./bootstrap && \
    ./configure --prefix=/usr/local && \
    make -j"$(nproc)" && \
    make install && \
    ldconfig && \
    rm -rf /tmp/libdaq

# download dan instal libml
RUN git clone https://github.com/snort3/libml.git /tmp/libml && \
    cd /tmp/libml && \
    ./configure.sh --prefix=/usr/local && \
    cd build && \
    make -j$(nproc) && \
    make install && \
    ldconfig && \
    mkdir -p /usr/local/src/libml && \
    cp -r /tmp/libml/examples /usr/local/src/libml/ && \
    rm -rf /tmp/libml 

# download dan instal snort
RUN git clone https://github.com/snort3/snort3.git /tmp/snort3 && \
    cd /tmp/snort3 && \
    ./configure_cmake.sh --prefix=/usr/local --enable-debug-msgs && \
    cd build && \
    make -j$(nproc) && \
    make install && \
    ldconfig && \
    rm -rf /tmp/snort3

# download dan instal snort-extra
RUN git clone https://github.com/snort3/snort3_extra.git /tmp/snort3_extra && \
    cd /tmp/snort3_extra && \
    ./configure_cmake.sh --prefix=/usr/local/ && \
    cd build && \
    make -j"$(nproc)" && \
    make install && \
    ldconfig && \
    rm -rf /tmp/snort3_extra

# buat direktori log
RUN mkdir -p /var/log/snort

# copy custom rules dan pcapgen
COPY rules/local.rules /usr/local/src/libml/examples/classifier/local.rules
COPY pcapgen.py /usr/local/src/libml/examples/classifier/pcapgen.py
