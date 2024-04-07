# To build the docker image:
# docker build -t devenvjekyll/ubuntu:0.1 .
#
# To create the docker container and open the terminal
# This will create a volume for us to persist our files. We can mount this volume in other containers later if we like.
#   The volume differs from mounting a directory from the host OS, in that docker will manage it and we won't access it from the host. 
#   That way we don't have to worry about line endings and such.
# I am also going to forward the container port 4000 to host port 4000, for when we serve the webpage in development
# docker run --name devenvjekyll --user developer -it -p 4000:4000 --mount type=volume,src=vol_devenvjekyll,dst=/home/developer/volume devenvjekyll/ubuntu:0.1 bash
# 
# When done, just 'exit'
# 
# To start the already created docker container and open the terminal
# docker start --attach --interactive devenvjekyll
#
# When done, just 'exit'

FROM ubuntu:22.04
SHELL ["/bin/bash", "-c"]
MAINTAINER ChristopherPisz
LABEL Description="Chris' Ubuntu based development environment for Jekyll"

ARG USERNAME=developer
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Install all the things we'll need
#    I am installing git on the container rather than the host, because we are using the volume rather than mounting a directory we share with the host
#    in my opinion, doing the latter invites all kinds of problems.
RUN apt update && apt -y --no-install-recommends install \
       sudo \
	   bash \
	   vim \
       git \
	   ssh \
       wget \
	   build-essential \
	   ruby-full \
	   zlib1g-dev

# Create a non-root user and give them sudo
RUN groupadd --gid $USER_GID $USERNAME && \
    useradd --uid $USER_UID --gid $USER_GID -m $USERNAME && \
	echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME && \
    chmod 0440 /etc/sudoers.d/$USERNAME

# Change to the user
USER $USERNAME
WORKDIR /home/$USERNAME

# As the user, make the directory we will map the volume to, as the user, so it has proper permissions
RUN mkdir -p /home/$USERNAME/volume

# As the user, create gem environment variables for the installation directory and create that directory
ENV GEM_HOME="/home/$USERNAME/gems"
ENV PATH="/home/$USERNAME/gems/bin:$PATH"

RUN mkdir -p /home/$USERNAME/gems/bin && \
    echo "export GEM_HOME=/home/$USERNAME/gems" >> ~/.bashrc && \
	echo "export PATH=/home/$USERNAME/gems/bin:$PATH" >> ~/.bashrc

# Install the need ruby gems
RUN	gem install jekyll bundler && \
    bundle add webrick

