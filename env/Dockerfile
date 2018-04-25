FROM centos:latest

# Replace shell with bash so we can source files
RUN rm /bin/sh && ln -s /bin/bash /bin/sh

# Add the Chrome repo
ADD google-chrome.repo /etc/yum.repos.d/

# Chrome dependencies
RUN yum -y install epel-release
RUN yum -y install libappindicator

# Install Chrome
RUN yum -y install google-chrome-stable

# Install Node via NVM (h/t https://gist.github.com/remarkablemark/aacf14c29b3f01d6900d13137b21db3a)
RUN curl --silent -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.10/install.sh | bash

# Install Node
ENV NVM_DIR /root/.nvm
ENV NODE_VERSION 8.11.1

# Install node and npm
RUN source $NVM_DIR/nvm.sh \
    && nvm install $NODE_VERSION \
    && nvm alias default $NODE_VERSION \
    && nvm use default

# Add node and npm to path so the commands are available
ENV NODE_PATH $NVM_DIR/v$NODE_VERSION/lib/node_modules
ENV PATH $NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH

# Confirm installation
RUN echo ${NODE_PATH} && echo ${PATH} && node -v
RUN npm -v

# Install Lighthouse
RUN npm i -g lighthouse
