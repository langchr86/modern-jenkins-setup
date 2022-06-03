modern-jenkins-setup
====================

[![vagrant-up](https://github.com/langchr86/modern-jenkins-setup/actions/workflows/vagrant-up.yml/badge.svg)](https://github.com/langchr86/modern-jenkins-setup/actions/workflows/vagrant-up.yml)

Example of a modern and easy to setup and maintain jenkins installation based on containers.



Disclaimer
----------

This is an example for a local jenkins installation.
I tried to use the most flexible, modern and easiest approach.
But this is still only one example of use cases and setup.
There may exist many other valid or even better approaches.


About
-----

This example project can be used as template for a completely scripted
and therefore easy maintainable jenkins instance.
The main technologies used are:

* Containers: Docker & docker-compose
* Reverse-proxy & file hosting: [Caddy2](https://caddyserver.com/v2)
* CI/CD: [Jenkins](https://www.jenkins.io/)
* Running Jenkins in Containers: [Jenkins in Docker](https://www.jenkins.io/doc/book/installing/docker/)
  based on: [Official Jenkins Docker image](https://github.com/jenkinsci/docker/blob/master/README.md)
* Scripted Jenkins Config: [Configuration as Code](https://plugins.jenkins.io/configuration-as-code/)
* Configurable jobs: [JobDSL](https://plugins.jenkins.io/job-dsl/)

The whole VM and ansible part can be used or removed for a real installation.
The core thing is the installation in containers.


Quick start
-----------

This project contains a completely scripted Virtualbox VM that hosts everything needed for this example jenkins setup.
The tools used are:

* VM Hypervisor: Virtualbox
* VM management: Vagrant
* Host Provisioning: Ansible

To run the VM we need a working installation of Virtualbox and Vagrant.
After that we can directly create and run the VM:

~~~~~~
vagrant up
~~~~~~

The VM creation and provisioning will take a while.
Even after the VM runs and provisioning has finished it may need some time until the docker images
have been created and the corresponding containers started.
After that we can access the services directly:

* [jenkins.localhost](https://jenkins.localhost/)
* [artifacts.localhost](https://artifacts.localhost/)

Because we use a local running instance of Caddy the insecure https certificate is expected.


### Credentials

Because we use SSH build agents we need some SSH credentials to connect to the agents.
Simply add the credential here: [Global Credentials](https://jenkins.localhost/credentials/store/system/domain/_/newCredentials)

* Select `SSH username and private key`.
* ID: `modern-jenkins`
* Username: `jenkins`
* Use the complete content of the following file as private key: [rsa-key-modern-jenkins-20220602.opriv](credentials/rsa-key-modern-jenkins-20220602.opriv)
* Passphrase: `modern`



Working with the VM
-------------------

The VM runs an Ubuntu OS with a Docker installation to run all containers.
To access the VM we can directly use the Virtualbox GUI, `vagrant ssh` or any other SSH agent like `PuTTY`.
The user and password is `vagrant`.

After connecting we can observe the logs of the jenkins docker-compose run:

~~~~~~
sudo journalctl -fu jenkins
~~~~~~

The main file for the whole setup is the [`docker-compose.yml`] file.
file located in `/etc/docker-compose/jenkins/docker-compose.yml`.
All other relevant files (e.g. the `Dockerfile`) are located under `/etc/jenkins/`.

If needed we can manipulate the deployed files by hand and restart the service:

~~~~~~
sudo systemctl restart jenkins
~~~~~~

This will start all 3 containers related to jenkins and defined in the [`docker-compose.yml`] file.



Debugging
---------

The first run of the containers need some time because of the download and build of the container images.
If the container build does fail or never finish the whole process can be started manually for easier debugging.
First stop the systemd service:

~~~~~~
sudo systemctl stop jenkins
~~~~~~

Then change to `/etc/docker-compose/jenkins` and start the process by hand:

~~~~~~
docker-compose up --build
~~~~~~



Update jenkins and plugins
--------------------------

All plugins and corresponding versions are collected in the [Controller-Dockerfile].
The easiest way to update to the newest versions is to first update all plugins.
To do so just open the jenkins web-UI and go to the [plugin manager](https://jenkins.localhost/pluginManager/) page.
Here we can see the newest version of each plugin.
Update those version strings for all plugins listend in the [Controller-Dockerfile].
Sometimes the name showed in the GUI do not directly match the name in the [Controller-Dockerfile].
The real raw name can be observed by clicking onto the link behind the GUI name.
After this we can restart the containers to see if everything is now up to date.
The easiest way to achieve this is to use Vagrant:

~~~~~~
vagrant provision
~~~~~~

The related ansible role will update all files in the VM and restart the Docker containers.

After the plugins work with the newest versions we can update the jenkins image itself.
The corresponding LTS base image can be found on [DockerHub](https://hub.docker.com/r/jenkins/jenkins/tags?page=1&name=lts-jdk11).
Now restart the containers again.

This described approach is useful to have fixed version numbers for an installation.
Jenkins itself provides various tools for automated updating.
See: [Preinstalling plugins](https://github.com/jenkinsci/docker/blob/master/README.md#preinstalling-plugins)



Docker DNS problems
-------------------

In some networks Docker-in-Docker may have problems to find the Google DNS servers.
In this case you can add an explicit IP for your network internal DNS in the [`daemon.json`] file.
For example like:

~~~~~~
"dns": ["172.18.0.100", "8.8.8.8", "8.8.4.4"]
~~~~~~



Documentation
-------------


### Jenkins Config as Code (JCasC)

* [configuration-as-code-plugin](https://github.com/jenkinsci/configuration-as-code-plugin/blob/master/README.md)
* [Tutorial](https://opensource.com/article/20/4/jcasc-jenkins)
* [View the current live config](https://jenkins.localhost/configuration-as-code/viewExport)


### Job DSL

Always use the own instance as reference for the syntax.
This will only show features/plugins that are available on this instance.

* [JobDSL-API](https://jenkins.localhost/plugin/job-dsl/api-viewer/index.html#)


### Declarative Pipelines

This is still groovy syntax but a more abstracted DSL to define Jenkins Pipelines.

* [Declarative Pipeline](https://www.jenkins.io/doc/book/pipeline/syntax/)
* [Agents](https://www.jenkins.io/doc/book/pipeline/syntax/#agent)
* [Pipeline Steps](https://www.jenkins.io/doc/pipeline/steps/)


### Useful features and plugins

* [copyartifact](https://www.jenkins.io/doc/pipeline/steps/copyartifact/)
* [build-discarder](https://plugins.jenkins.io/build-discarder/)
* [docker-workflow](https://www.jenkins.io/doc/pipeline/steps/docker-workflow/)
* [Docker Pipeline](https://www.jenkins.io/doc/book/pipeline/docker/)


### Nodes, Agents and Executors

* [Managing Nodes](https://www.jenkins.io/doc/book/managing/nodes/)
* [Base docker image for SSH connected agents](https://hub.docker.com/r/jenkins/ssh-agent)
* [Inbound Agent in Docker](https://github.com/jenkinsci/remoting/blob/master/docs/inbound-agent.md)
* [Instance Identity](https://jenkins.localhost/instance-identity/)



Most relevant Files
-------------------

* [Controller-Dockerfile]
* [`docker-compose.yml`]
* [`daemon.json`]

[Controller-Dockerfile]: ansible/roles/jenkins/files/dockerfiles/jenkins-controller/Dockerfile
[`docker-compose.yml`]: ansible/roles/jenkins/templates/docker-compose.yml
[`daemon.json`]: ansible/roles/jenkins/files/docker/daemon.json
