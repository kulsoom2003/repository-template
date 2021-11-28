##########################################
# parameters
##########################################

# [Choice] Ubuntu version: bionic, focal
ARG VARIANT="focal"

##########################################
# base image
##########################################

# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.154.0/containers/ubuntu/.devcontainer/base.Dockerfile
FROM mcr.microsoft.com/vscode/devcontainers/base:0-${VARIANT}

##########################################
# context
##########################################

WORKDIR /devcontainer/docker-context
COPY /postCreate.ps1 ./

WORKDIR /devcontainer/docker-build-workdir
USER root

##########################################
# repository setup
##########################################

RUN sudo apt-get update \
    && sudo apt-get install -y wget apt-transport-https software-properties-common \
    && wget -q "https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb" \
    && sudo dpkg -i packages-microsoft-prod.deb 

# RUN sudo add-apt-repository universe
RUN sudo apt-get update 

##########################################
# powershell
##########################################

RUN sudo apt-get install -y powershell \
    && pwsh --version

RUN pwsh -noni -nol -nop -c Install-Module PSFzf -Scope AllUsers -Force

SHELL ["/usr/bin/pwsh", "-nop", "-nol", "-c"]

##########################################
# core utilities
##########################################

RUN sudo apt-get install -y file

RUN sudo apt-get install -y tmux vim ranger fzf \
    && tmux -V  \
    && fzf --version

##########################################
# network utilities
##########################################

RUN sudo apt install -y iputils-ping dnsutils socat ipcalc
RUN echo "wireshark-common wireshark-common/install-setuid boolean true" | sudo debconf-set-selections
RUN sudo apt install -y wireshark tshark termshark

##########################################
# npm
##########################################

RUN sudo apt install -y npm \
    && npm version

##########################################
# docker
##########################################

RUN sudo apt-get install -y docker.io \
    && docker --version

RUN iwr "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-Linux-x86_64" -outfile /usr/local/bin/docker-compose \
    && chmod +x /usr/local/bin/docker-compose \
    && docker-compose version

##########################################
# github
##########################################

RUN iwr "https://github.com/cli/cli/releases/download/v2.0.0/gh_2.0.0_linux_amd64.deb" -outfile gh.deb \
    && dpkg -i gh.deb \
    && gh version

##########################################
# python
##########################################

RUN sudo apt install -y python3-pip python3.8
RUN python3 --version \
    && pip3 --version

RUN pip3 install virtualenv black pandas numpy

##########################################
# vscode
##########################################

RUN npm install -g vsce \
  && vsce --version

##########################################
# user
##########################################

RUN groupadd -r containergroup && useradd -r -g containergroup --create-home containeruser
RUN usermod -aG docker containeruser
RUN usermod -aG docker vscode
USER root

##########################################
# entrypoint 
##########################################

ENTRYPOINT [ "/bin/pwsh", "-nop", "-nol", "-c"]
CMD [ "while ($true) {start-sleep 30}" ]
WORKDIR /