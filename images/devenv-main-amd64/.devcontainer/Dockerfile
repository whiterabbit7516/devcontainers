##########################################
# parameters
##########################################

# ARG VARIANT="focal"

##########################################
# base image
##########################################

FROM --platform=linux/amd64 mcr.microsoft.com/devcontainers/base:ubuntu
ARG INVALIDATECACHE="counter-7"

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
RUN sudo apt-get update \
  && sudo apt-get install -y wget apt-transport-https software-properties-common \
  && wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb \
  && sudo dpkg -i packages-microsoft-prod.deb 

# hashicorp
RUN curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add - \
  && sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"

# RUN sudo add-apt-repository universe
COPY etc.apt.preferences /etc/apt/preferences
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

RUN sudo apt-get install -y tmux vim ranger fzf file pciutils direnv \
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
# rust
##########################################

RUN curl https://sh.rustup.rs -sSf | sh -s -- -y
RUN /root/.cargo/bin/cargo version
RUN sudo apt-get install -y pkg-config

##########################################
# dotnet
##########################################

RUN sudo apt-get install -y dotnet-sdk-8.0 dotnet-sdk-7.0 \
  && dotnet --list-sdks \
  && dotnet --version

##########################################
# java
##########################################

RUN sudo apt-get install -y openjdk-8-jdk \
  && javac -version

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

RUN npm install -g pnpm \
    && pnpm --version

##########################################
# typescript
##########################################

RUN sudo npm install -g typescript ts-node \
  && tsc --version \
  && ts-node --version

##########################################
# go
##########################################

RUN iwr https://go.dev/dl/go1.21.6.linux-amd64.tar.gz -outfile go.tar.gz \
  && tar -C /usr/local -xzf ./go.tar.gz \
  && /usr/local/go/bin/go version

##########################################
# python
##########################################

RUN sudo apt install -y python3-pip python3.10
RUN python3 --version \
  && pip3 --version

RUN pip3 install virtualenv black pandas numpy

##########################################
# devcontainers
##########################################

RUN sudo npm install -g @devcontainers/cli \
  && devcontainer --version

##########################################
# foundry
##########################################

RUN iwr https://github.com/foundry-rs/foundry/releases/download/nightly/foundry_nightly_linux_amd64.tar.gz -outfile foundry.tar.gz \
  && mkdir foundry \
  && tar -C ./foundry -xzf foundry.tar.gz \
  && mv ./foundry /usr/local/foundry/ \
  && /usr/local/foundry/forge --version

##########################################
# scripts
##########################################

WORKDIR /devcontainer/docker-context

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
