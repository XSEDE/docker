# Dockerfile to create an environment that contains the Nix package manager.

# Note: changing to CentOS as a base: https://hub.docker.com/_/centos
FROM centos:7

# Enable HTTPS support in wget and set nsswitch.conf to make resolution work within containers
RUN yum install -y -q wget openssl \
  && echo hosts: dns files > /etc/nsswitch.conf

# Download Nix and install it into the system.
RUN wget -q https://nixos.org/releases/nix/nix-2.3/nix-2.3-x86_64-linux.tar.xz \
  && tar xf nix-2.3-x86_64-linux.tar.xz \
  && groupadd -g 30000 nixbld \
  && for i in $(seq 1 30); do adduser -r -d /var/empty -u $((30000 + i)) -g nixbld nixbld$i ; done \
  && mkdir -m 0755 /etc/nix \
  && echo 'sandbox = false' > /etc/nix/nix.conf \
  && echo "build-users-group =" >> /etc/nix/nix.conf \
  && mkdir -m 0755 /nix && USER=root sh nix-*-x86_64-linux/install \
  && ln -s /nix/var/nix/profiles/default/etc/profile.d/nix.sh /etc/profile.d/ \
  && rm -r /nix-*-x86_64-linux* \
  && rm -rf /var/cache/apk/* \
  && /nix/var/nix/profiles/default/bin/nix-collect-garbage --delete-old \
  && /nix/var/nix/profiles/default/bin/nix-store --optimise \
  && /nix/var/nix/profiles/default/bin/nix-store --verify --check-contents

ONBUILD ENV \
    ENV=/etc/profile \
    USER=root \
    PATH=/nix/var/nix/profiles/default/bin:/nix/var/nix/profiles/default/sbin:/bin:/sbin:/usr/bin:/usr/sbin \
    GIT_SSL_CAINFO=/etc/pki/ca-trust/extracted/pem/tls-ca-bundle.pem \
    NIX_SSL_CERT_FILE=/etc/pki/ca-trust/extracted/pem/tls-ca-bundle.pem

ENV \
    ENV=/etc/profile \
    USER=root \
    PATH=/nix/var/nix/profiles/default/bin:/nix/var/nix/profiles/default/sbin:/bin:/sbin:/usr/bin:/usr/sbin \
    GIT_SSL_CAINFO=/etc/pki/ca-trust/extracted/pem/tls-ca-bundle.pem \
    NIX_SSL_CERT_FILE=/etc/pki/ca-trust/extracted/pem/tls-ca-bundle.pem \
    NIX_PATH=/nix/var/nix/profiles/per-user/root/channels

RUN nix-channel --add https://nixos.org/channels/nixpkgs-unstable nixpkgs
RUN nix-channel --update

RUN nix-build -A pythonFull '<nixpkgs>'

