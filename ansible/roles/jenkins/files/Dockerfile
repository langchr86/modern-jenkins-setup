FROM jenkins/jenkins:2.332.3-lts-jdk11

USER root

RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common

# prepare folder for artifact publishing
RUN mkdir -p /var/www/jenkins && chown jenkins:jenkins -R /var/www/jenkins

USER jenkins
RUN jenkins-plugin-cli --plugins \
  build-discarder:60.v1747b0eb632a \
  build-timestamp:1.0.3 \
  build-user-vars-plugin:1.8 \
  command-launcher:81.v9c2cb_cb_db_392 \
  configuration-as-code:1429.v09b_044a_c93de \
  copyartifact:1.46.4 \
  docker-workflow:1.28 \
  git:4.11.3 \
  htmlpublisher:1.30 \
  jaxb:2.3.6-1 \
  job-dsl:1.79 \
  pipeline-build-step:2.18 \
  pipeline-stage-view:2.24 \
  pipeline-utility-steps:2.12.1 \
  rebuild:1.34 \
  ssh-agent:295.v9ca_a_1c7cc3a_a_ \
  throttle-concurrents:2.8 \
  workflow-aggregator:581.v0c46fa_697ffd \
  workflow-multibranch:716.vc692a_e52371b_ \
