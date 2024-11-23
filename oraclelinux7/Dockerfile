FROM docker.io/oraclelinux:7
LABEL org.opencontainers.image.source=https://github.com/nakahiro386/docker-systemd

# Detected virtualization docker
ENV container=docker
VOLUME ["/sys/fs/cgroup", "/tmp", "/run"]

# couldn't find an alternative telinit implementation to spawn.
# CMD ["/usr/sbin/init"]
CMD ["/usr/lib/systemd/systemd", "--system"]

# https://www.freedesktop.org/software/systemd/man/systemd.html#Signals
STOPSIGNAL SIGRTMIN+3

RUN set -x \
 && yum makecache fast \
 && yum -y -q install deltarpm \
 && yum -y -q upgrade \
 && yum -y -q install \
      sudo \
      openssh-server \
 && yum -q clean all \
 && (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*; \
rm -f /lib/systemd/system/anaconda.target.wants/*; \
 sed -i -e 's/UsePAM yes/UsePAM no/' /etc/ssh/sshd_config \
 && sed -i -e 's/^\(session.*pam_loginuid.so\)/#\1/g' /etc/pam.d/sshd \
 && systemctl enable sshd.service \
 && systemctl set-default multi-user.target \
 && systemctl disable rhnsd.service

ARG EXEC_UID="1000"
ARG EXEC_GID="1000"
ARG EXEC_USER="ansible"
ARG EXEC_GROUP="ansible"
ARG EXEC_USER_PASSWD="ansible"

RUN set -x \
 && groupadd -g ${EXEC_GID} ${EXEC_GROUP} \
 && useradd -m -u ${EXEC_UID} -g ${EXEC_GID} ${EXEC_USER} \
 && echo "${EXEC_USER}:${EXEC_USER_PASSWD}" | chpasswd \
 && usermod -aG wheel ${EXEC_USER} \
 && echo "${EXEC_USER} ALL=NOPASSWD: ALL" > /etc/sudoers.d/${EXEC_USER}

EXPOSE 22
