FROM jenkins/jenkins:2.319.1-lts-jdk11

USER root

RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common

RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
RUN apt-key fingerprint 0EBFCD88
RUN add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    docker-ce-cli

# prepare folder for artifact publishing
RUN mkdir -p /var/www/jenkins && chown jenkins:jenkins -R /var/www/jenkins

USER jenkins
RUN jenkins-plugin-cli --plugins "\
  build-discarder:60.v1747b0eb632a \
  build-timestamp:1.0.3 \
  build-user-vars-plugin:1.8 \
  command-launcher:1.6 \
  configuration-as-code:1.55 \
  copyartifact:1.46.2 \
  docker-workflow:1.26 \
  git:4.10.1 \
  htmlpublisher:1.28 \
  jaxb:2.3.0.1 \
  job-dsl:1.78.3 \
  pipeline-build-step:2.15 \
  pipeline-stage-view:2.20 \
  pipeline-utility-steps:2.11.0 \
  rebuild:1.32 \
  ssh-agent:1.23 \
  throttle-concurrents:2.6 \
  workflow-aggregator:2.6 \
  workflow-multibranch:2.26 \
  "
