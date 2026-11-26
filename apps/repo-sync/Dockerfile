FROM ubuntu:26.04
RUN DEBIAN_FRONTEND=noninteractive \
    && apt-get update \
    && apt-get install --no-install-recommends -y \
        ca-certificates \
        curl \
        libarchive-tools \
    && apt clean \
    && rm -rf /var/lib/apt/lists/*
