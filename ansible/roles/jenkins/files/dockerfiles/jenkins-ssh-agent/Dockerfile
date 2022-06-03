FROM jenkins/ssh-agent:latest-bullseye-jdk11

RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common \
  && rm -rf /var/lib/apt/lists/*

# prepare folder for artifact publishing
RUN mkdir -p /var/www/jenkins && chown jenkins:jenkins -R /var/www/jenkins

# install docker CLI to be used in docker-workflow plugin
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
RUN apt-key fingerprint 0EBFCD88
RUN add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    docker-ce-cli \
  && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /var/jenkins/agent-1 \
  && mkdir -p /var/jenkins/agent-2 \
  && chown -R jenkins:jenkins /var/jenkins/ \
