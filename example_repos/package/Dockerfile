FROM ubuntu:22.04

# make dpkg noninteractive
ENV DEBIAN_FRONTEND=noninteractive DEBCONF_NONINTERACTIVE_SEEN=true

# install all needed packages
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        zip \
    && rm -rf /var/lib/apt/lists/*
