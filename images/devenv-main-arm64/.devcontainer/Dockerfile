##########################################
# parameters
##########################################

ARG INVALIDATE_CACHE="counter-10"
ARG IMAGE_VARIANT="ubuntu-24.04"

##########################################
# base image
##########################################

FROM --platform=linux/arm64 mcr.microsoft.com/devcontainers/base:${IMAGE_VARIANT}

##########################################
# context
##########################################

WORKDIR /devcontainer/build/
USER root

##########################################
# repository setup
##########################################

# apt tools
RUN sudo apt-get update -q \
  && sudo apt-get install -y -q wget apt-transport-https software-properties-common

# microsoft
RUN wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb \
  && sudo dpkg -i packages-microsoft-prod.deb 
# temporary until base:ubuntu-24.10 is available
RUN sudo add-apt-repository -y ppa:dotnet/backports

# hashicorp
RUN wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

# kubernetes
RUN apt-get install -y -q apt-transport-https ca-certificates curl \
    && curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg \
    && chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg \
    && echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list

# teleport
RUN sudo curl https://apt.releases.teleport.dev/gpg -o /usr/share/keyrings/teleport-archive-keyring.asc \
    && . /etc/os-release \
    && echo "deb [signed-by=/usr/share/keyrings/teleport-archive-keyring.asc] https://apt.releases.teleport.dev/${ID?} ${VERSION_CODENAME?} stable/v16" | sudo tee /etc/apt/sources.list.d/teleport.list > /dev/null

# 1password
RUN curl -sS https://downloads.1password.com/linux/keys/1password.asc | sudo gpg --dearmor --output /usr/share/keyrings/1password-archive-keyring.gpg \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/1password-archive-keyring.gpg] https://downloads.1password.com/linux/debian/$(dpkg --print-architecture) stable main" | sudo tee /etc/apt/sources.list.d/1password.list \
  && sudo mkdir -p /etc/debsig/policies/AC2D62742012EA22/ \
  && curl -sS https://downloads.1password.com/linux/debian/debsig/1password.pol | sudo tee /etc/debsig/policies/AC2D62742012EA22/1password.pol \
  && sudo mkdir -p /usr/share/debsig/keyrings/AC2D62742012EA22 \
  && curl -sS https://downloads.1password.com/linux/keys/1password.asc | sudo gpg --dearmor --output /usr/share/debsig/keyrings/AC2D62742012EA22/debsig.gpg

# RUN sudo add-apt-repository universe
# COPY etc.apt.preferences /etc/apt/preferences
RUN sudo apt-get update -q

##########################################
# powershell
##########################################

RUN curl -L -o /tmp/powershell.tar.gz https://github.com/PowerShell/PowerShell/releases/download/v7.3.4/powershell-7.3.4-linux-arm64.tar.gz \
    && sudo mkdir -p /opt/microsoft/powershell/7 \
    && sudo tar zxf /tmp/powershell.tar.gz -C /opt/microsoft/powershell/7 \
    && sudo chmod +x /opt/microsoft/powershell/7/pwsh \
    && sudo ln -s /opt/microsoft/powershell/7/pwsh /usr/bin/pwsh \
    && pwsh --version

SHELL ["/usr/bin/pwsh", "-nop", "-nol", "-c"]

##########################################
# core utilities
##########################################

RUN sudo apt-get install -y -q tmux vim ranger fzf file pciutils direnv \
    && tmux -V \
    && fzf --version

##########################################
# network utilities
##########################################

RUN sudo apt-get install -y -q iputils-ping dnsutils socat ipcalc
RUN echo "wireshark-common wireshark-common/install-setuid boolean true" | sudo debconf-set-selections
RUN sudo apt-get install -y -q tshark termshark

##########################################
# rust
##########################################

RUN curl https://sh.rustup.rs -sSf | sh -s -- -y
RUN /root/.cargo/bin/cargo version
RUN sudo apt-get install -y -q pkg-config

##########################################
# dotnet
##########################################

RUN sudo apt-get install -y -q dotnet-sdk-9.0 dotnet-sdk-8.0 \
  && dotnet --list-sdks \
  && dotnet --version

##########################################
# java
##########################################

# RUN sudo apt-get install -y -q openjdk-8-jdk \
#   && javac -version

##########################################
# npm
##########################################

RUN sudo apt-get install -y -q npm \
    && npm version

RUN npm cache clean -f \
    && npm install -g n \
    && n stable

RUN npm install --global yarn pnpm yo \
  && yarn --version \
  && pnpm --version \
  && yo --version

##########################################
# typescript
##########################################

RUN sudo npm install -g typescript ts-node \
  && tsc --version \
  && ts-node --version

##########################################
# go
##########################################

RUN iwr https://go.dev/dl/go1.21.6.linux-arm64.tar.gz -outfile go.tar.gz \
  && tar -C /usr/local -xzf ./go.tar.gz \
  && /usr/local/go/bin/go version

##########################################
# python
##########################################

RUN sudo apt-get install -y -q python3.12 python3-pip
RUN python3 --version \
      && pip3 --version

##########################################
# vscode
##########################################

RUN sudo npm install -g @devcontainers/cli \
    && devcontainer --version

RUN sudo npm install -g generator-code \
    && yo --generators

##########################################
# 1password
##########################################

RUN sudo apt install -y 1password-cli \
    && op --version

##########################################
# hashicorp
##########################################

# terraform
RUN sudo apt-get install -y -q terraform \
    && terraform version

# vault
RUN sudo apt-get install -y -q vault \
    && sudo apt-get install --reinstall -y -q vault \
    && vault version

# nomad
RUN sudo apt-get install -y -q nomad \
    && nomad version

# consul
RUN sudo apt-get install -y -q nomad \
    && nomad version

##########################################
# docker
##########################################

RUN sudo apt-get install -y -q docker.io \
    && docker --version

##########################################
# aws
##########################################

RUN iwr "https://awscli.amazonaws.com/awscli-exe-linux-aarch64.zip" -outfile "awscliv2.zip" \
    && unzip -q awscliv2.zip \
    && sudo ./aws/install \
    && rm -rf aws* \
    && aws --version

##########################################
# kubernetes
##########################################

RUN sudo apt-get install -y -q kubectl \
    && kubectl version --client

# k9s
RUN iwr https://github.com/derailed/k9s/releases/download/v0.32.7/k9s_Linux_arm64.tar.gz -outfile k9s.tar.gz \
    && tar xzf k9s.tar.gz k9s \
    && mv k9s /usr/local/bin/k9s \
    && k9s version

# kubectx
RUN iwr https://github.com/ahmetb/kubectx/releases/download/v0.9.4/kubectx_v0.9.4_linux_arm64.tar.gz -outfile kubectx.tar.gz \
    && tar xzf kubectx.tar.gz kubectx \
    && mv kubectx /usr/local/bin/kubectx \
    && kubectx --help

# kubens
RUN iwr https://github.com/ahmetb/kubectx/releases/download/v0.9.4/kubens_v0.9.4_linux_arm64.tar.gz -outfile kubens.tar.gz \
    && tar xzf kubens.tar.gz kubens \
    && mv kubens /usr/local/bin/kubens \
    && kubens --help

# kubeseal
RUN iwr https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.28.0/kubeseal-0.28.0-linux-arm.tar.gz -outfile kubeseal.tar.gz \
    && tar xzf kubeseal.tar.gz kubeseal \
    && chmod +x ./kubeseal \
    && mv kubeseal /usr/local/bin/kubeseal 
    # && kubeseal --version # error: .buildkit_qemu_emulator: /usr/local/bin/kubeseal: Invalid ELF image for this architecture

# helm
RUN iwr https://get.helm.sh/helm-v3.17.0-linux-arm64.tar.gz -outfile helm.tar.gz \
    && tar xzf helm.tar.gz linux-arm64/helm \
    && mv linux-arm64/helm /usr/local/bin/helm \
    && helm version

# argo cd
RUN iwr https://github.com/argoproj/argo-cd/releases/download/v2.7.2/argocd-linux-arm64 -outfile argocd \
    && chmod +x ./argocd \
    && mv ./argocd /usr/local/bin/argocd \
    && argocd version --client

##########################################
# teleport
##########################################

RUN sudo apt-get install -y -q teleport \
    && teleport version \
    && tsh version \
    && tctl version

##########################################
# grpc
##########################################

# grpcurl
RUN iwr https://github.com/fullstorydev/grpcurl/releases/download/v1.8.7/grpcurl_1.8.7_linux_arm64.tar.gz -outfile grpcurl.tar.gz \
    && tar xzf grpcurl.tar.gz \
    && chmod +x ./grpcurl \
    && mv ./grpcurl /usr/local/bin/grpcurl \
    && grpcurl -version

# grpcui
RUN iwr https://github.com/fullstorydev/grpcui/releases/download/v1.3.1/grpcui_1.3.1_linux_arm64.tar.gz -outfile grpcui.tar.gz \
    && tar xzf grpcui.tar.gz \
    && chmod +x ./grpcui \
    && mv ./grpcui /usr/local/bin/grpcui \
    && grpcui -version

# protobuf libs
RUN sudo apt-get install -y -q protobuf-compiler protobuf-compiler-grpc libprotobuf-dev

##########################################
# postgres
##########################################

RUN sudo apt-get install -y -q postgresql-16 \
  && psql --version

##########################################
# foundry
##########################################

RUN iwr https://github.com/foundry-rs/foundry/releases/download/nightly/foundry_nightly_linux_arm64.tar.gz -outfile foundry.tar.gz \
  && mkdir foundry \
  && tar -C ./foundry -xzf foundry.tar.gz \
  && mv ./foundry /usr/local/foundry/ \
  && /usr/local/foundry/forge --version

##########################################
# aztec/noir
##########################################

# nargo
RUN curl -fsSL https://raw.githubusercontent.com/noir-lang/noirup/refs/heads/main/install | bash \
    && /root/.nargo/bin/noirup \
    && /root/.nargo/bin/nargo --version

# # bb
# RUN apt-get install -y -q libc++-dev \
#     && curl -fsSL https://raw.githubusercontent.com/AztecProtocol/aztec-packages/refs/heads/master/barretenberg/bbup/install | bash \
#     && /root/.bb/bbup --noir-version 1.0.0-beta.2 \
#     && /root/.bb/bb --version

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
