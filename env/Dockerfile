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
