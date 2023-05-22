##########################################
# parameters
##########################################

# ARG VARIANT="focal"

##########################################
# base image
##########################################

# FROM mcr.microsoft.com/devcontainers/universal:2-linux
FROM mcr.microsoft.com/devcontainers/base:ubuntu

##########################################
# users
##########################################

# RUN groupadd -r containergroup && useradd -r -g containergroup --create-home containeruser

##########################################
# context
##########################################

WORKDIR /devcontainer/docker-build-workdir
USER root

##########################################
# repository setup
##########################################

# microsoft
# RUN sudo apt-get update \
#   && sudo apt-get install -y wget apt-transport-https software-properties-common \
#   && wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb \
#   && sudo dpkg -i packages-microsoft-prod.deb 

##########################################
# dotnet
##########################################

RUN sudo apt-get update
RUN sudo apt-get install -y dotnet-sdk-7.0
RUN dotnet --list-sdks
RUN dotnet --version

# hashicorp
RUN curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add - \
  && sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"

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

RUN sudo apt-get install -y tmux vim ranger fzf file \
  && tmux -V \
  && fzf --version

##########################################
# network utilities
##########################################

RUN sudo apt install -y iputils-ping dnsutils socat ipcalc
RUN echo "wireshark-common wireshark-common/install-setuid boolean true" | sudo debconf-set-selections
RUN sudo apt install -y wireshark tshark termshark

##########################################
# linuxbrew
##########################################

# RUN git clone https://github.com/Homebrew/brew ~/.linuxbrew/Homebrew \
#     && mkdir ~/.linuxbrew/bin \
#     && ln -s ~/.linuxbrew/Homebrew/bin/brew ~/.linuxbrew/bin \
#     && ln -s ~/.linuxbrew/Homebrew/bin/brew /usr/local/bin/ \
#     && /bin/bash -c eval $(~/.linuxbrew/bin/brew shellenv) \
#     && brew update

##########################################
# python
##########################################

RUN sudo apt install -y python3-pip python3.10
RUN python3 --version \
  && pip3 --version

RUN pip3 install virtualenv black pandas numpy

##########################################
# rust
##########################################

RUN curl https://sh.rustup.rs -sSf | sh -s -- -y
RUN /root/.cargo/bin/cargo version
RUN sudo apt-get install -y pkg-config

##########################################
# dotnet
##########################################

RUN sudo apt-get install -y dotnet-sdk-7.0
RUN dotnet --list-sdks
RUN dotnet --version

##########################################
# npm
##########################################

RUN sudo apt install -y npm \
  && npm version

RUN npm cache clean -f \
  && npm install -g n \
  && n stable

RUN npm install --global yarn \
  && yarn --version

##########################################
# typescript
##########################################

RUN sudo npm install -g typescript \
  && tsc --version

##########################################
# devcontainers
##########################################

RUN sudo npm install -g @devcontainers/cli \
  && devcontainer --version

##########################################
# scripts
##########################################

WORKDIR /devcontainer/docker-context
COPY /postCreate.ps1 ./

##########################################
# user
##########################################

USER root

##########################################
# entrypoint 
##########################################

ENTRYPOINT [ "/bin/pwsh", "-nop", "-nol", "-c"]
CMD [ "while ($true) {start-sleep 30}" ]
WORKDIR /
