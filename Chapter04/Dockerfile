# Use Ubuntu 20.04 LTS as the base image
FROM ubuntu:20.04

# Avoid prompts from apt
ENV DEBIAN_FRONTEND=noninteractive

# Install required packages
RUN apt-get update && apt-get install -y \
    wget \
    xz-utils \
    libncursesw5 \
    software-properties-common \
    make \
    git \
    gnupg \
    && add-apt-repository ppa:deadsnakes/ppa \
    && apt-get update \
    && apt-get install -y python3.8 python3.8-distutils \
    && rm -rf /var/lib/apt/lists/*

# Set Python 3.8 as the default python3
RUN update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.8 1

# Install graph-cli via pip
RUN apt-get update \
    && apt-get install -y python3-pip \
    && pip3 install graph-cli \
    && rm -rf /var/lib/apt/lists/*

# Install latest CMake from Kitware
RUN wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc | gpg --dearmor -o /usr/share/keyrings/kitware-archive-keyring.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/kitware-archive-keyring.gpg] https://apt.kitware.com/ubuntu/ focal main" > /etc/apt/sources.list.d/kitware.list \
    && apt-get update \
    && apt-get install -y cmake \
    && rm -rf /var/lib/apt/lists/*

# Install latest g++ (C++23 capable)
RUN add-apt-repository -y ppa:ubuntu-toolchain-r/test \
    && apt-get update \
    && apt-get install -y g++-13 \
    && update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-13 100 \
    && rm -rf /var/lib/apt/lists/*

# Download and extract arm-none-eabi-gcc
RUN wget -O arm-gnu-toolchain.tar.xz "https://developer.arm.com/-/media/Files/downloads/gnu/13.2.rel1/binrel/arm-gnu-toolchain-13.2.rel1-x86_64-arm-none-eabi.tar.xz?rev=e434b9ea4afc4ed7998329566b764309&hash=CA590209F5774EE1C96E6450E14A3E26" \
    && tar -xf arm-gnu-toolchain.tar.xz -C /opt/ \
    && rm arm-gnu-toolchain.tar.xz

# Download and unpack Renode
RUN wget https://github.com/renode/renode/releases/download/v1.15.0/renode-1.15.0.linux-portable.tar.gz \
    && tar -xzvf renode-1.15.0.linux-portable.tar.gz -C /opt/ \
    && rm renode-1.15.0.linux-portable.tar.gz

# Add the toolchain and Renode bin directories to PATH
ENV PATH="/opt/arm-gnu-toolchain-13.2.Rel1-x86_64-arm-none-eabi/bin:/opt/renode_1.15.0_portable:${PATH}"

# Install Bazelisk (renamed as 'bazel')
RUN wget https://github.com/bazelbuild/bazelisk/releases/download/v1.25.0/bazelisk-linux-amd64 \
    && chmod +x bazelisk-linux-amd64 \
    && mv bazelisk-linux-amd64 /usr/local/bin/bazel

# Create new user 'constexpr'
RUN useradd -m constexpr

# Create and set working directory, ensuring it is owned by constexpr
RUN mkdir -p /workspace && chown constexpr:constexpr /workspace
WORKDIR /workspace

# Switch to non-root user 'constexpr'
USER constexpr

# Run Bazel to trigger Bazelisk to download Bazel and its dependencies.
RUN bazel version

# Keep the container running
CMD ["sleep", "infinity"]
