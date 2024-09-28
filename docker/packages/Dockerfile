# Use the base image Ubuntu 24.04
FROM ubuntu:24.04 AS builder

# Set environment variables
ENV LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    VERSION=6.10.0 \
    PKG_VERSION=1 \
    DEBIAN_FRONTEND=noninteractive \
    DEB_BUILD_OPTIONS=nostrip \
    SCONSFLAGS="sunstone=yes context=yes fireedge=yes systemd=no rubygems=yes download=yes "

# Update packages and install dependencies
RUN apt-get update
RUN apt-get install -y --install-recommends \
    wget \
    curl \
    jq \
    xz-utils \
    debhelper \
    bash-completion \
    bison \
    default-jdk \
    flex \
    javahelper \
    libmysql++-dev \
    libsqlite3-dev \
    libssl-dev \
    libxml2-dev \
    libxslt1-dev \
    libcurl4-openssl-dev \
    libsystemd-dev \
    libvncserver-dev \
    libnsl-dev \
    python3-setuptools \
    ruby \
    scons \
    ruby-dev \
    rake \
    libaugeas-dev \
    python3-pip \
    unzip \
    libcairo2-dev \
    libossp-uuid-dev \
    freerdp2-dev \
    libssh2-1-dev \
    libpango1.0-dev \
    libpulse-dev \
    libwebp-dev \
    libvorbis-dev \
    default-libmysqlclient-dev \
    nodejs \
    libnode-dev \
    npm \
    libzmq5 \
    libzmq3-dev \
    ronn

# Install additional development packages
RUN apt install -y build-essential checkinstall libncursesw5-dev libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev libffi-dev

# Download, build, and install Python 2.7.18
RUN wget -qO- https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz | tar -xzvf - -C . && \
    cd Python-2.7.18 && \
    ./configure --enable-optimizations && \
    make -j $(nproc) && \
    make install && \
    python -V

# Install Node Version Manager (nvm) and Node.js
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash && \
    . ~/.nvm/nvm.sh && nvm install v14.21.3 && \
    npm install -g bower grunt grunt-cli && \
    git clone --recursive https://github.com/sass/node-sass.git /node-sass && \
    cd node-sass && \
    git checkout tags/v4.14.1 && \
    npm install && \
    node scripts/build -f


# Create a working directory
WORKDIR /opennebula-${VERSION}

# Download and extract the source code of OpenNebula version 6.10.0
RUN git clone https://github.com/OpenNebula/one /opennebula-${VERSION} && \
    cd /opennebula-${VERSION} && \
    git checkout tags/release-${VERSION}

# Build man
RUN cd ./share/man && ./build.sh

# Create a tarball of the OpenNebula source and prepare for building the package
RUN tar -czf /opennebula_${VERSION}.orig.tar.gz /opennebula-${VERSION} && \
    OS_NAME=$(grep "^NAME=" /etc/os-release | cut -d= -f2 | tr -d '"') && \
    OS_RELEASE=$(grep VERSION_ID /etc/os-release | cut -d= -f2 | tr -d '"') && \
    echo "Detected OS: $OS_NAME" && \
    echo "Detected OS Release: $OS_RELEASE" && \
    echo "Downloading from: https://downloads.opennebula.io/repo/${VERSION}/${OS_NAME}/${OS_RELEASE}/opennebula-${VERSION}-${PKG_VERSION}-amd64/source/opennebula_${VERSION}-${PKG_VERSION}.debian.tar.xz" && \
    wget -qO- https://downloads.opennebula.io/repo/${VERSION}/${OS_NAME}/${OS_RELEASE}/opennebula-${VERSION}-${PKG_VERSION}-amd64/source/opennebula_${VERSION}-${PKG_VERSION}.debian.tar.xz | tar -xJf - -C . && \
    echo "10" > ./debian/compat && \
    mkdir ../sources && \
    tar -xzf ./build_opennebula.tar.gz -C ../sources/

# Build the Debian package
RUN . ~/.nvm/nvm.sh && dpkg-buildpackage -us -uc

# Start a new stage to preserve only the packages
FROM ubuntu:24.04
WORKDIR /packages
# Copy the built packages from the builder stage
COPY --from=builder /*.tar.xz /*.deb /packages/
