# Ansible Managed Node - ARM64 Compatible
FROM ubuntu:22.04

# Avoid interactive prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Install essential packages for Ansible managed node
RUN apt-get update && apt-get install -y \
    openssh-server \
    python3 \
    python3-pip \
    sudo \
    curl \
    wget \
    vim \
    nano \
    net-tools \
    iputils-ping \
    htop \
    systemctl \
    rsyslog \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Create ansible user with sudo privileges
RUN useradd -m -s /bin/bash ansible && \
    echo 'ansible:ansible123' | chpasswd && \
    usermod -aG sudo ansible && \
    echo 'ansible ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

# Create a regular user for testing
RUN useradd -m -s /bin/bash testuser && \
    echo 'testuser:testpass' | chpasswd

# Configure SSH
RUN mkdir /var/run/sshd && \
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/' /etc/ssh/sshd_config

# Set root password for testing (optional)
RUN echo 'root:rootpass' | chpasswd

# Create SSH directory for ansible user
RUN mkdir -p /home/ansible/.ssh && \
    chown ansible:ansible /home/ansible/.ssh && \
    chmod 700 /home/ansible/.ssh

# Create a sample web directory for testing file operations
RUN mkdir -p /var/www/html && \
    echo "<h1>Ansible Managed Node</h1><p>This server is managed by Ansible</p>" > /var/www/html/index.html && \
    chown -R ansible:ansible /var/www/html

# Create some sample directories and files for Ansible practice
RUN mkdir -p /opt/ansible-test && \
    echo "sample config file" > /opt/ansible-test/config.txt && \
    chown -R ansible:ansible /opt/ansible-test

# Install Python packages commonly used with Ansible
RUN pip3 install \
    PyYAML \
    Jinja2 \
    httplib2 \
    six

# Expose SSH port
EXPOSE 22

# Create entrypoint script
RUN echo '#!/bin/bash\n\
# Start SSH service\n\
service ssh start\n\
\n\
# Start rsyslog for logging\n\
service rsyslog start\n\
\n\
# Keep container running\n\
tail -f /dev/null' > /entrypoint.sh && \
chmod +x /entrypoint.sh

# Set the entrypoint
ENTRYPOINT ["/entrypoint.sh"]
