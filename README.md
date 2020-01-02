
This repo contains Dockerfiles to install the [Nix package manager](https://nixos.org/nix/) to Alpine and CentOS images.  Dockerfile_alpine is an unchanged copy of the Dockerfile from the original repo by NixOS, and the CentOS version is based off of it but developed by us.

## For CentOS

Clone this repo, `cd` to it, run `docker build - < Dockerfile_centos`.  You can make changes to the Dockerfile and rebuild in the same manner.

---
## For Alpine

Use this build to create your own customized images as follows:

    FROM nixos/nix

    RUN nix-channel --add https://nixos.org/channels/nixpkgs-unstable nixpkgs
    RUN nix-channel --update

    RUN nix-build -A pythonFull '<nixpkgs>'
