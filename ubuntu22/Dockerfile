# =============================================================================
# Ubuntu 22.04 VM - Jammy Jellyfish
# =============================================================================
FROM ubuntu:22.04

LABEL maintainer="Hermes Agent" \
      description="Ubuntu 22.04 VM-like container with SSH and dev tools" \
      version="22.04"

# Avoid interactive prompts during package installs
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC

# Set up locales
RUN apt-get update && apt-get install -y --no-install-recommends \
    tzdata \
    locales \
    && locale-gen en_US.UTF-8 \
    && update-locale LANG=en_US.UTF-8 \
    && rm -rf /var/lib/apt/lists/*

ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8

# Install essential packages — VM-like toolset
RUN apt-get update && apt-get install -y --no-install-recommends \
    # SSH server
    openssh-server \
    # Shell & terminal
    bash \
    zsh \
    tmux \
    # Networking
    curl \
    wget \
    net-tools \
    iproute2 \
    iputils-ping \
    dnsutils \
    traceroute \
    # Dev tools
    git \
    vim \
    nano \
    build-essential \
    gcc \
    g++ \
    make \
    cmake \
    # Python
    python3 \
    python3-pip \
    python3-dev \
    # System utilities
    sudo \
    htop \
    tree \
    unzip \
    zip \
    rsync \
    jq \
    # Libraries
    libssl-dev \
    libffi-dev \
    && rm -rf /var/lib/apt/lists/*

# Set up SSH
RUN mkdir -p /var/run/sshd \
    && echo "root:ubuntu" | chpasswd \
    && sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config \
    && sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config \
    && echo "export VISIBLE=now" >> /etc/profile

# Create a non-root user 'dev' with sudo
RUN useradd -m -s /bin/bash dev \
    && echo "dev:dev" | chpasswd \
    && usermod -aG sudo dev \
    && echo "dev ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/dev

# Welcome message
RUN echo '\n\e[32m╔══════════════════════════════════════╗\n\
║   Ubuntu 22.04 VM Container          ║\n\
║   User: dev / Pass: dev              ║\n\
║   Root: root / Pass: ubuntu          ║\n\
╚══════════════════════════════════════╝\e[0m\n' > /etc/motd

WORKDIR /home/dev

# Expose SSH port
EXPOSE 22

# Start SSH daemon
CMD ["/usr/sbin/sshd", "-D"]