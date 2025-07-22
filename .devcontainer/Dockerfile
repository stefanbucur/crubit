FROM debian:bookworm-slim

ARG BAZELISK_VERSION=1.26.0
ARG CLANG_VERSION=20

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    build-essential \
    ca-certificates \
    curl \
    git-all \
    python3 \
    zlib1g-dev \
    && update-ca-certificates \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    gnupg \
    lsb-release \
    software-properties-common \
    wget \
    && wget https://apt.llvm.org/llvm.sh && chmod +x llvm.sh && ./llvm.sh ${CLANG_VERSION} && rm ./llvm.sh \
    && rm -rf /var/lib/apt/lists/*

# Install latest Bazelisk (universal Bazel launcher).
RUN curl -f -L https://github.com/bazelbuild/bazelisk/releases/download/v${BAZELISK_VERSION}/bazelisk-linux-amd64 \
    -o /usr/local/bin/bazelisk \
    && chmod +x /usr/local/bin/bazelisk \
    && ln -sf /usr/local/bin/bazelisk /usr/local/bin/bazel

ENV PATH="/usr/local/bin:${PATH}"
