##########################################
# parameters
##########################################

# ARG VARIANT="focal"

##########################################
# base image
##########################################

FROM --platform=linux/arm64 mcr.microsoft.com/devcontainers/base:ubuntu
ARG INVALIDATECACHE="counter-6"

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
    && sudo dpkg -i packages-microsoft-prod.deb \
    && apt-get update

# hashicorp
RUN wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list \
    && apt-get update

# kubernetes
RUN apt-get install -y apt-transport-https ca-certificates curl \
    && curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg \
    && chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg \
    && echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list \
    && apt-get update

# teleport
RUN sudo curl https://apt.releases.teleport.dev/gpg -o /usr/share/keyrings/teleport-archive-keyring.asc \
    && . /etc/os-release \
    && echo "deb [signed-by=/usr/share/keyrings/teleport-archive-keyring.asc] https://apt.releases.teleport.dev/${ID?} ${VERSION_CODENAME?} stable/v13" \
    | sudo tee /etc/apt/sources.list.d/teleport.list > /dev/null \
    && apt-get update

# RUN sudo add-apt-repository universe
RUN sudo apt-get update 

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

RUN sudo apt-get install -y tmux vim ranger fzf file pciutils direnv \
    && tmux -V \
    && fzf --version

##########################################
# network utilities
##########################################

RUN sudo apt install -y iputils-ping dnsutils socat ipcalc
RUN echo "wireshark-common wireshark-common/install-setuid boolean true" | sudo debconf-set-selections
RUN sudo apt install -y tshark termshark

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

RUN sudo apt-get install -y dotnet-sdk-6.0 dotnet-sdk-7.0 \
  && dotnet --list-sdks \
  && dotnet --version

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

RUN iwr https://go.dev/dl/go1.21.6.linux-arm64.tar.gz -outfile go.tar.gz \
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
# aws
##########################################

RUN iwr "https://awscli.amazonaws.com/awscli-exe-linux-aarch64.zip" -outfile "awscliv2.zip" \
    && unzip awscliv2.zip \
    && sudo ./aws/install \
    && rm -rf aws* \
    && aws --version

# RUN sudo pwsh -c Install-Module -Scope AllUsers -Name AWSPowerShell.NetCore -Force

##########################################
# vault
##########################################

RUN sudo apt-get install -y vault \
    && sudo apt-get install --reinstall -y vault \
    && vault version

##########################################
# terraform
##########################################

RUN sudo apt-get install -y terraform \
    && terraform version

##########################################
# kubernetes
##########################################

RUN sudo apt-get install -y kubectl \
    && kubectl version --client

# k9s
RUN iwr https://github.com/derailed/k9s/releases/download/v0.27.4/k9s_Linux_arm64.tar.gz -outfile k9s.tar.gz \
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
RUN iwr https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.21.0/kubeseal-0.21.0-linux-arm.tar.gz -outfile kubeseal.tar.gz \
    && tar xzf kubeseal.tar.gz kubeseal \
    && chmod +x ./kubeseal \
    && mv kubeseal /usr/local/bin/kubeseal
    # && kubeseal --version

# helm
RUN iwr https://get.helm.sh/helm-v3.12.0-linux-arm64.tar.gz -outfile helm.tar.gz \
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

RUN sudo apt-get install -y teleport \
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
RUN sudo apt install -y protobuf-compiler protobuf-compiler-grpc libprotobuf-dev

##########################################
# postgres
##########################################

RUN sudo apt install -y postgresql-14 \
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
