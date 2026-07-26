# =============================================================================
# Cyberguard - Ubuntu 20.04 CIS Benchmark Assessment Target
# =============================================================================
# Purpose:
#   Ubuntu 20.04 container designed to act as an SSH-accessible
#   CIS Benchmark assessment target for the Cyberguard FYP.
#
# IMPORTANT:
#   This image installs security/auditing packages and utilities.
#   It does NOT automatically make the system CIS compliant.
#
#   Cyberguard should connect over SSH and inspect:
#     - Package installation
#     - SSH configuration
#     - Password policies
#     - PAM configuration
#     - Audit configuration
#     - Logging
#     - Firewall configuration
#     - File permissions
#     - User/group configuration
#     - AppArmor
#     - Automatic updates
#     - Other CIS controls
# =============================================================================

FROM ubuntu:20.04

LABEL maintainer="Hermes Agent" \
      description="Ubuntu 20.04 CIS Benchmark Assessment Target" \
      version="20.04-cis"

# =============================================================================
# Environment
# =============================================================================

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC

# =============================================================================
# Locale and Timezone
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
# Core System Packages
# =============================================================================

RUN apt-get update && apt-get install -y --no-install-recommends \
    # -------------------------------------------------------------------------
    # SSH / Remote Administration
    # -------------------------------------------------------------------------
    openssh-server \
    openssh-client \
    ssh \
    sshpass \
    
    # -------------------------------------------------------------------------
    # Shells / Terminal
    # -------------------------------------------------------------------------
    bash \
    zsh \
    tmux \
    
    # -------------------------------------------------------------------------
    # Networking / Network Auditing
    # -------------------------------------------------------------------------
    curl \
    wget \
    net-tools \
    iproute2 \
    iputils-ping \
    dnsutils \
    traceroute \
    nmap \
    tcpdump \
    ethtool \
    lsof \
    
    # -------------------------------------------------------------------------
    # Firewall / Network Security
    # -------------------------------------------------------------------------
    ufw \
    iptables \
    iptables-persistent \
    nftables \
    
    # -------------------------------------------------------------------------
    # Authentication / PAM / Password Security
    # -------------------------------------------------------------------------
    libpam-pwquality \
    libpam-tmpdir \
    libpam-modules \
    libpam-runtime \
    passwd \
    cracklib-runtime \
    
    # -------------------------------------------------------------------------
    # Auditing
    # -------------------------------------------------------------------------
    auditd \
    audispd-plugins \
    acct \
    
    # -------------------------------------------------------------------------
    # Mandatory Access Control
    # -------------------------------------------------------------------------
    apparmor \
    apparmor-utils \
    apparmor-profiles \
    apparmor-profiles-extra \
    
    # -------------------------------------------------------------------------
    # Intrusion Prevention
    # -------------------------------------------------------------------------
    fail2ban \
    
    # -------------------------------------------------------------------------
    # File Integrity Monitoring
    # -------------------------------------------------------------------------
    aide \
    aide-common \
    
    # -------------------------------------------------------------------------
    # Logging / Log Management
    # -------------------------------------------------------------------------
    rsyslog \
    logrotate \
    systemd \
    
    # -------------------------------------------------------------------------
    # Automatic Security Updates
    # -------------------------------------------------------------------------
    unattended-upgrades \
    update-notifier-common \
    
    # -------------------------------------------------------------------------
    # System Administration
    # -------------------------------------------------------------------------
    sudo \
    cron \
    systemd-sysv \
    dbus \
    
    # -------------------------------------------------------------------------
    # File / Permission / System Inspection
    # -------------------------------------------------------------------------
    coreutils \
    findutils \
    procps \
    psmisc \
    util-linux \
    file \
    acl \
    attr \
    xz-utils \
    gzip \
    tar \
    
    # -------------------------------------------------------------------------
    # Security / Cryptography
    # -------------------------------------------------------------------------
    openssl \
    ca-certificates \
    gnupg \
    gnupg2 \
    gpg \
    debsums \
    
    # -------------------------------------------------------------------------
    # System Utilities
    # -------------------------------------------------------------------------
    htop \
    tree \
    unzip \
    zip \
    rsync \
    jq \
    vim \
    nano \
    
    # -------------------------------------------------------------------------
    # Development Tools
    # -------------------------------------------------------------------------
    git \
    build-essential \
    gcc \
    g++ \
    make \
    cmake \
    
    # -------------------------------------------------------------------------
    # Python
    # -------------------------------------------------------------------------
    python3 \
    python3-pip \
    python3-dev \
    
    # -------------------------------------------------------------------------
    # Libraries
    # -------------------------------------------------------------------------
    libssl-dev \
    libffi-dev \
    
    && rm -rf /var/lib/apt/lists/*

# =============================================================================
# SSH Configuration
# =============================================================================

RUN mkdir -p /var/run/sshd \
    && mkdir -p /run/sshd \
    \
    # Root password (assessment lab only)
    && echo "root:ubuntu" | chpasswd \
    \
    # Enable SSH password authentication for the lab
    && sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin yes/' \
        /etc/ssh/sshd_config \
    \
    && sed -i 's/^#\?PasswordAuthentication.*/PasswordAuthentication yes/' \
        /etc/ssh/sshd_config \
    \
    # Disable empty passwords
    && sed -i 's/^#\?PermitEmptyPasswords.*/PermitEmptyPasswords no/' \
        /etc/ssh/sshd_config \
    \
    # SSH protocol
    && echo "Protocol 2" >> /etc/ssh/sshd_config \
    \
    # Display login banner
    && echo "export VISIBLE=now" >> /etc/profile

# =============================================================================
# Create Non-Root Administrative User
# =============================================================================

RUN useradd \
        --create-home \
        --shell /bin/bash \
        dev \
    && echo "dev:dev" | chpasswd \
    && usermod -aG sudo dev \
    && echo "dev ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/dev \
    && chmod 0440 /etc/sudoers.d/dev

# =============================================================================
# Security Directories
# =============================================================================

RUN mkdir -p \
    /var/log/audit \
    /var/log/journal \
    /etc/audit/rules.d \
    /etc/ssh/sshd_config.d \
    /etc/security \
    /etc/fail2ban \
    /var/lib/aide

# =============================================================================
# Configure rsyslog
# =============================================================================

RUN systemctl enable rsyslog 2>/dev/null || true

# =============================================================================
# Configure auditd
# =============================================================================

RUN systemctl enable auditd 2>/dev/null || true

# =============================================================================
# Configure AppArmor
# =============================================================================

RUN systemctl enable apparmor 2>/dev/null || true

# =============================================================================
# Configure Fail2Ban
# =============================================================================

RUN systemctl enable fail2ban 2>/dev/null || true

# =============================================================================
# Configure Cron
# =============================================================================

RUN systemctl enable cron 2>/dev/null || true

# =============================================================================
# Configure Log Rotation
# =============================================================================

RUN chmod 0755 /etc/logrotate.d \
    && chmod 0644 /etc/logrotate.conf

# =============================================================================
# Configure Unattended Upgrades
# =============================================================================

RUN mkdir -p /etc/apt/apt.conf.d \
    && printf '%s\n' \
    'APT::Periodic::Update-Package-Lists "1";' \
    'APT::Periodic::Unattended-Upgrade "1";' \
    > /etc/apt/apt.conf.d/20auto-upgrades

# =============================================================================
# Basic Password Policy
# =============================================================================
# NOTE:
# These settings are intentionally moderate.
# For your Cyberguard FYP, you can modify these to create:
#   1. CIS-compliant target
#   2. CIS-non-compliant target
# and test whether Cyberguard detects the differences.
# =============================================================================

RUN sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS   365/' /etc/login.defs \
    && sed -i 's/^PASS_MIN_DAYS.*/PASS_MIN_DAYS   1/' /etc/login.defs \
    && sed -i 's/^PASS_WARN_AGE.*/PASS_WARN_AGE   7/' /etc/login.defs

# =============================================================================
# Configure Password Quality
# =============================================================================

RUN if grep -q "^password.*pam_pwquality.so" /etc/pam.d/common-password; then \
        sed -i 's/pam_pwquality.so.*/pam_pwquality.so retry=3 minlen=14/' \
        /etc/pam.d/common-password; \
    else \
        echo "password requisite pam_pwquality.so retry=3 minlen=14" \
        >> /etc/pam.d/common-password; \
    fi

# =============================================================================
# Welcome Message
# =============================================================================

RUN printf '%s\n' \
    '' \
    '╔══════════════════════════════════════════════════════════════╗' \
    '║        Cyberguard CIS Benchmark Assessment Target          ║' \
    '║        Ubuntu 20.04 - Focal Fossa                          ║' \
    '║                                                              ║' \
    '║  SSH User: dev                                               ║' \
    '║  SSH Password: dev                                           ║' \
    '║  Root Password: ubuntu                                       ║' \
    '║                                                              ║' \
    '║  Security Tools:                                             ║' \
    '║    - auditd                                                  ║' \
    '║    - AppArmor                                                ║' \
    '║    - UFW / iptables / nftables                              ║' \
    '║    - AIDE                                                    ║' \
    '║    - Fail2Ban                                                ║' \
    '║    - rsyslog                                                 ║' \
    '║    - unattended-upgrades                                     ║' \
    '║    - PAM / pwquality                                         ║' \
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