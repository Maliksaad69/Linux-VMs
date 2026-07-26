# =============================================================================
# Cyberguard - Ubuntu 22.04 Mixed Security / Vulnerable Assessment Target
# =============================================================================
# Purpose:
#   A deliberately mixed-security Ubuntu 22.04 target for testing Cyberguard.
#
#   This machine contains:
#     - Some security packages installed
#     - Some security controls partially configured
#     - Some CIS controls intentionally misconfigured
#     - Some security packages completely missing
#
#   Expected Cyberguard Results:
#     PASS  -> Some controls
#     FAIL  -> Some controls
#     WARN  -> Some controls
#
# WARNING:
#   This image is intentionally insecure.
#   Use ONLY inside an isolated lab / development environment.
# =============================================================================

FROM ubuntu:22.04

LABEL maintainer="Cyberguard" \
      description="Ubuntu 22.04 Mixed Security CIS Assessment Target" \
      version="22.04-mixed"

# =============================================================================
# Environment
# =============================================================================

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC

# =============================================================================
# Locale
# =============================================================================

RUN apt-get update && apt-get install -y --no-install-recommends \
    tzdata \
    locales \
    && locale-gen en_US.UTF-8 \
    && update-locale LANG=en_US.UTF-8 \
    && ln -fs /usr/share/zoneinfo/UTC /etc/localtime \
    && dpkg-reconfigure -f noninteractive tzdata \
    && rm -rf /var/lib/apt/lists/*

ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV LC_ALL=en_US.UTF-8

# =============================================================================
# Install MIXED Security Packages
# =============================================================================
#
# INTENTIONALLY INSTALLED:
#   - auditd
#   - rsyslog
#   - sudo
#   - ufw
#   - AppArmor utilities
#   - unattended-upgrades
#
# INTENTIONALLY NOT INSTALLED:
#   - AIDE
#   - Fail2Ban
#   - libpam-pwquality
#   - iptables-persistent
#
# This allows Cyberguard to detect both:
#   - Installed security controls
#   - Missing security controls
# =============================================================================

RUN apt-get update && apt-get install -y --no-install-recommends \
    # -------------------------------------------------------------------------
    # SSH
    # -------------------------------------------------------------------------
    openssh-server \
    openssh-client \
    
    # -------------------------------------------------------------------------
    # Shell
    # -------------------------------------------------------------------------
    bash \
    zsh \
    tmux \
    
    # -------------------------------------------------------------------------
    # Networking
    # -------------------------------------------------------------------------
    curl \
    wget \
    net-tools \
    iproute2 \
    iputils-ping \
    dnsutils \
    traceroute \
    lsof \
    
    # -------------------------------------------------------------------------
    # Firewall - Installed
    # -------------------------------------------------------------------------
    ufw \
    iptables \
    nftables \
    
    # -------------------------------------------------------------------------
    # Auditing - Installed
    # -------------------------------------------------------------------------
    auditd \
    audispd-plugins \
    
    # -------------------------------------------------------------------------
    # Logging - Installed
    # -------------------------------------------------------------------------
    rsyslog \
    logrotate \
    
    # -------------------------------------------------------------------------
    # AppArmor - Installed
    # -------------------------------------------------------------------------
    apparmor \
    apparmor-utils \
    
    # -------------------------------------------------------------------------
    # Automatic Updates - Installed
    # -------------------------------------------------------------------------
    unattended-upgrades \
    
    # -------------------------------------------------------------------------
    # System Utilities
    # -------------------------------------------------------------------------
    sudo \
    cron \
    procps \
    psmisc \
    util-linux \
    findutils \
    coreutils \
    file \
    acl \
    attr \
    htop \
    tree \
    vim \
    nano \
    unzip \
    zip \
    rsync \
    jq \
    
    # -------------------------------------------------------------------------
    # Cryptography
    # -------------------------------------------------------------------------
    openssl \
    ca-certificates \
    gnupg \
    
    # -------------------------------------------------------------------------
    # Development
    # -------------------------------------------------------------------------
    git \
    build-essential \
    gcc \
    g++ \
    make \
    
    # -------------------------------------------------------------------------
    # Python
    # -------------------------------------------------------------------------
    python3 \
    python3-pip \
    
    && rm -rf /var/lib/apt/lists/*

# =============================================================================
# SSH CONFIGURATION
# =============================================================================
#
# INTENTIONAL CIS VIOLATIONS:
#
#   1. Root login enabled
#   2. Password authentication enabled
#   3. Weak SSH crypto configuration
#   4. SSH idle timeout not configured
#   5. Max authentication attempts not hardened
#
# This allows Cyberguard to test SSH CIS checks.
# =============================================================================

RUN mkdir -p /var/run/sshd \
    && mkdir -p /run/sshd \
    \
    # Weak root password
    && echo "root:root" | chpasswd \
    \
    # Enable root SSH login
    && sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin yes/' \
        /etc/ssh/sshd_config \
    \
    # Enable password authentication
    && sed -i 's/^#\?PasswordAuthentication.*/PasswordAuthentication yes/' \
        /etc/ssh/sshd_config \
    \
    # Allow empty passwords - INTENTIONAL VULNERABILITY
    && sed -i 's/^#\?PermitEmptyPasswords.*/PermitEmptyPasswords yes/' \
        /etc/ssh/sshd_config \
    \
    # Weak authentication configuration
    && echo "MaxAuthTries 10" >> /etc/ssh/sshd_config \
    \
    # No SSH idle timeout
    && echo "# ClientAliveInterval intentionally not configured" \
        >> /etc/ssh/sshd_config

# =============================================================================
# Create Non-Root User
# =============================================================================
#
# INTENTIONAL ISSUES:
#
#   - User has password authentication
#   - User has unrestricted passwordless sudo
#
# =============================================================================

RUN useradd \
        --create-home \
        --shell /bin/bash \
        dev \
    && echo "dev:dev" | chpasswd \
    && usermod -aG sudo dev \
    \
    # INTENTIONAL CIS VIOLATION:
    # Passwordless unrestricted sudo
    && echo "dev ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/dev \
    && chmod 0440 /etc/sudoers.d/dev

# =============================================================================
# PASSWORD POLICY - INTENTIONALLY WEAK
# =============================================================================
#
# CIS violations:
#   - Weak password aging
#   - Password expiration disabled
#   - No password quality enforcement
#   - No minimum password length enforcement
# =============================================================================

RUN sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS   99999/' /etc/login.defs \
    && sed -i 's/^PASS_MIN_DAYS.*/PASS_MIN_DAYS   0/' /etc/login.defs \
    && sed -i 's/^PASS_WARN_AGE.*/PASS_WARN_AGE   0/' /etc/login.defs

# =============================================================================
# Disable Password Complexity
# =============================================================================
#
# libpam-pwquality is intentionally NOT installed.
#
# Cyberguard should detect that password complexity enforcement
# is missing.
# =============================================================================

# =============================================================================
# Firewall - INSTALLED BUT DISABLED
# =============================================================================
#
# UFW is installed but intentionally disabled.
#
# We do not execute `ufw disable` during Docker build because iptables
# operations require NET_ADMIN privileges that are unavailable during
# normal Docker image builds.
#
# Cyberguard can inspect /etc/ufw/ufw.conf remotely over SSH.
# =============================================================================

RUN sed -i 's/^ENABLED=.*/ENABLED=no/' /etc/ufw/ufw.conf

# =============================================================================
# Auditd - INSTALLED BUT WEAKLY CONFIGURED
# =============================================================================
#
# auditd is installed but no comprehensive CIS audit rules are provided.
#
# Expected:
#   Package check      -> PASS
#   Audit rules check  -> FAIL
# =============================================================================

RUN mkdir -p /etc/audit/rules.d \
    && printf '%s\n' \
    '# Cyberguard intentionally incomplete audit configuration' \
    '# CIS audit rules are missing' \
    > /etc/audit/rules.d/cyberguard.rules

# =============================================================================
# Rsyslog - INSTALLED
# =============================================================================
#
# Logging is available.
# However, remote logging is not configured.
#
# This creates a MIXED state.
# =============================================================================

RUN mkdir -p /var/log \
    && touch /var/log/auth.log \
    && touch /var/log/syslog

# =============================================================================
# AppArmor - INSTALLED
# =============================================================================
#
# AppArmor package is installed.
# However, profiles are not actively enforced in this container.
#
# Expected:
#   Package installed -> PASS
#   Enforcement       -> WARN / FAIL
# =============================================================================

RUN mkdir -p /etc/apparmor.d

# =============================================================================
# Automatic Updates - PARTIALLY CONFIGURED
# =============================================================================
#
# Package installed, but automatic security updates are not explicitly
# configured.
# =============================================================================

RUN mkdir -p /etc/apt/apt.conf.d \
    && printf '%s\n' \
    'APT::Periodic::Update-Package-Lists "7";' \
    > /etc/apt/apt.conf.d/20auto-upgrades

# =============================================================================
# File Permissions - INTENTIONALLY WEAK
# =============================================================================
#
# Some sensitive files are deliberately given weak permissions.
#
# These are safe for testing CIS permission checks.
# =============================================================================

RUN chmod 0644 /etc/shadow \
    && chmod 0644 /etc/gshadow \
    && chmod 0644 /etc/passwd \
    && chmod 0644 /etc/group

# =============================================================================
# World-Writable Test Directory
# =============================================================================
#
# Used to test filesystem permission checks.
# =============================================================================

RUN mkdir -p /opt/test-data \
    && chmod 0777 /opt/test-data \
    && touch /opt/test-data/test.txt \
    && chmod 0666 /opt/test-data/test.txt

# =============================================================================
# Unnecessary / Unused Service Simulation
# =============================================================================
#
# Creates a sample configuration file that Cyberguard can detect
# as an unnecessary or insecure service configuration.
# =============================================================================

RUN mkdir -p /etc/test-service \
    && printf '%s\n' \
    'enabled=true' \
    'authentication=false' \
    'allow_anonymous=true' \
    > /etc/test-service/config.conf \
    && chmod 0644 /etc/test-service/config.conf

# =============================================================================
# Weak Kernel / Network Configuration
# =============================================================================
#
# These values are intentionally NOT hardened.
#
# Note:
# Docker containers share the host kernel, so sysctl settings may not
# actually be changeable from inside the container.
#
# Cyberguard should distinguish:
#   - Container-level configuration
#   - Host-level configuration
# =============================================================================

RUN printf '%s\n' \
    '# Cyberguard test configuration' \
    '# IPv4 forwarding is intentionally not hardened' \
    '# ICMP redirects are intentionally not hardened' \
    > /etc/sysctl.d/99-cyberguard-test.conf

# =============================================================================
# Cron Permissions - INTENTIONALLY WEAK
# =============================================================================

RUN chmod 0666 /etc/crontab

# =============================================================================
# SSH Banner
# =============================================================================

RUN printf '%s\n' \
    '╔══════════════════════════════════════════════════════════════╗' \
    '║      Cyberguard Mixed Security Assessment Target           ║' \
    '║      Ubuntu 22.04 - INTENTIONALLY MISCONFIGURED            ║' \
    '║                                                              ║' \
    '║  SSH User: dev                                               ║' \
    '║  SSH Password: dev                                           ║' \
    '║  Root Password: root                                         ║' \
    '║                                                              ║' \
    '║  Security State: MIXED                                      ║' \
    '║                                                              ║' \
    '║  Some controls: INSTALLED                                   ║' \
    '║  Some controls: MISCONFIGURED                               ║' \
    '║  Some controls: MISSING                                     ║' \
    '╚══════════════════════════════════════════════════════════════╝' \
    '' \
    > /etc/motd

# =============================================================================
# Working Directory
# =============================================================================

WORKDIR /home/dev

# =============================================================================
# SSH Port
# =============================================================================

EXPOSE 22

# =============================================================================
# Start SSH
# =============================================================================

CMD ["/usr/sbin/sshd", "-D", "-e"]