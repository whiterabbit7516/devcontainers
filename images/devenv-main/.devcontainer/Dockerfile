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

RUN groupadd -r containergroup && useradd -r -g containergroup --create-home containeruser

##########################################
# context
##########################################

WORKDIR /devcontainer/docker-build-workdir
USER root
