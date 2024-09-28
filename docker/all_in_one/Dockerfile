# Use the base image Ubuntu 24.04
FROM ubuntu:24.04

# Set environment variables
ENV LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    DEBIAN_FRONTEND=noninteractive 

# Define an argument for OpenNebula version with a default value
ARG OPENNEBULA_VERSION=6.10.0-1

# Set the working directory
WORKDIR /var/lib/one

# Copy the Debian packages into the working directory
COPY ./packages/*.deb ./

# Copy the supervisor configuration
COPY ./docker/all_in_one/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Update the package list and install required packages
RUN apt update && apt install -y \
    apt-utils \
    systemd \
    supervisor \
    netcat-traditional mysql-client \
    nano mc net-tools \
    ./libopennebula-java_${OPENNEBULA_VERSION}_all.deb \
    ./libopennebula-java-doc_${OPENNEBULA_VERSION}_all.deb \
    ./opennebula_${OPENNEBULA_VERSION}_arm64.deb \
    ./opennebula-common_${OPENNEBULA_VERSION}_all.deb \
    ./opennebula-common-onecfg_${OPENNEBULA_VERSION}_all.deb \
    ./opennebula-dbgsym_${OPENNEBULA_VERSION}_arm64.deb \
    ./opennebula-fireedge_${OPENNEBULA_VERSION}_arm64.deb \
    ./opennebula-flow_${OPENNEBULA_VERSION}_all.deb \
    ./opennebula-gate_${OPENNEBULA_VERSION}_all.deb \
    ./opennebula-guacd_${OPENNEBULA_VERSION}_arm64.deb \
    ./opennebula-libs_${OPENNEBULA_VERSION}_all.deb \
    ./opennebula-node-kvm_${OPENNEBULA_VERSION}_all.deb \
    # ./opennebula-node-lxc_${OPENNEBULA_VERSION}_arm64.deb \
    ./opennebula-provision_${OPENNEBULA_VERSION}_all.deb \
    ./opennebula-provision-data_${OPENNEBULA_VERSION}_all.deb \
    ./opennebula-rubygems_${OPENNEBULA_VERSION}_arm64.deb \
    ./opennebula-sunstone_${OPENNEBULA_VERSION}_all.deb \
    ./opennebula-tools_${OPENNEBULA_VERSION}_all.deb \
    ./python3-pyone_${OPENNEBULA_VERSION}_all.deb \
    && apt-get -y clean \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf ./*.deb \
    && mkdir -p /var/run/sshd

# Set the entry point to supervisor
CMD ["/usr/bin/supervisord"]