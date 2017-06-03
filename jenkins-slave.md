The following are personal notes on how to install a Jenkins slave capable of running Docker.

All instructions are for **hobbyists**. Use at own risk.

## Description

The server on which this is installed on is meant to be exclusively a Jenkins slave node. Nothing else. Do not install anything else on this slave.

Docker is installed for its capability of executing Jenkinsfile steps inside containers.

The slave itself is not run within a docker container, because aspirin costs more than a cheap server.

## Create public private key pair

- Create Jenkins slave public/private key pair on Jenkins host
- Add private key in the Jenkins host as "jenkins" user
  - (Top) Jenkins > Manage Jenkins
  - (Left) Credentials
  - (Body) System > Global credentials
  - (Left) Add Credentials
  - (Body)
    - Kind: SSH Username with private key
    - Username: `jenkins`
    - *No restrictions on other fields.*

## Setup slave

Assuming fresh install of headless Ubuntu 16.04.2 LTS.

These are not scripts, do not put in a file and run blindly.

### Add jenkins user
    
    # Create Jenkins user
    useradd -m jenkins
    
    # Create Jenkins slave public/private key pair on Jenkins host
    # then add private key in the credentials of the Jenkins host using "jenkins" as the username
    # then install public key of the Jenkins slave
    sudo -u jenkins mkdir /home/jenkins/.ssh
    sudo -u jenkins vim /home/jenkins/.ssh/authorized_keys
    
### Install the rest and restart

    # Update host
    apt-get update
    apt-get upgrade
    
    # Install Java 8 through third-party PPA
    # source https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-get-on-ubuntu-16-04
    sudo apt-get install default-jre
    add-apt-repository ppa:webupd8team/java
    apt-get install openjdk-8-jre
    apt-get install openjdk-8-jdk
    
    # Install git
    apt-get install git
    
    # Install docker
    # source https://docs.docker.com/engine/installation/linux/ubuntu/
    apt-get install linux-image-extra-$(uname -r) linux-image-extra-virtual
    sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    apt-key fingerprint 0EBFCD88
    sudo add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    apt-get update
    apt-get install docker-ce
    
    docker run hello-world
    
    # Add jenkins to docker group
    usermod -a -G docker jenkins
    
    # Restart
    shutdown -r now
