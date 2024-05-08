FROM docker.io/ubuntu:24.04

# Detected virtualization docker
ENV container=docker
VOLUME ["/sys/fs/cgroup", "/tmp", "/run"]

CMD ["/lib/systemd/systemd", "--system"]

# https://www.freedesktop.org/software/systemd/man/systemd.html#Signals
STOPSIGNAL SIGRTMIN+3

RUN set -x \
 && export DEBIAN_FRONTEND=noninteractive \
 && apt-get update -q \
 && apt-get upgrade -y -q --auto-remove \
 && apt-get install -y -q --no-install-recommends --auto-remove \
    dbus \
    systemd \
    systemd-cron \
    rsyslog \
    iproute2 \
    sudo \
    openssh-server \
 && apt-get clean -y -q \
 && rm -Rf /var/lib/apt/lists/* \
 && rm -f /etc/systemd/system/*.wants/* \
 && systemctl enable ssh.service \
 && systemctl set-default multi-user.target

ARG EXEC_UID="1001"
ARG EXEC_GID="1001"
ARG EXEC_USER="ansible"
ARG EXEC_GROUP="ansible"
ARG EXEC_USER_PASSWD="ansible"

RUN set -x \
 && groupadd -g ${EXEC_GID} ${EXEC_GROUP} \
 && useradd -m -s /bin/bash -u ${EXEC_UID} -g ${EXEC_GID} ${EXEC_USER} \
 && echo "${EXEC_USER}:${EXEC_USER_PASSWD}" | chpasswd \
 && usermod -aG sudo ${EXEC_USER} \
 && echo "${EXEC_USER} ALL=NOPASSWD: ALL" > /etc/sudoers.d/${EXEC_USER}

EXPOSE 22
