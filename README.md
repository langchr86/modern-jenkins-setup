modern-jenkins-setup
====================

[![vagrant-up](https://github.com/langchr86/modern-jenkins-setup/actions/workflows/vagrant-up.yml/badge.svg)](https://github.com/langchr86/modern-jenkins-setup/actions/workflows/vagrant-up.yml)

Example of a modern and easy to setup and maintain jenkins installation based on containers.



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

All plugins and corresponding versions are collected in the [`Dockerfile`].
The easiest way to update to the newest versions is to first update all plugins.
To do so just open the jenkins web-UI and go to the [plugin manager](https://jenkins.localhost/pluginManager/) page.
Here we can see the newest version of each plugin.
Update those version strings for all plugins listend in the [`Dockerfile`].
Sometimes the name showed in the GUI do not directly match the name in the [`Dockerfile`].
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



[`Dockerfile`]: ansible/roles/jenkins/files/Dockerfile
[`docker-compose.yml`]: ansible/roles/jenkins/templates/docker-compose.yml
[`daemon.json`]: ansible/roles/jenkins/files/docker/daemon.json
