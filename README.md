# k8s-cicd-pipeline-jenkins
This is a simple project about creating a Kubernetes CI/CD pipeline with Jenkins

We choose to deploy a Java application

# The Pipeline

The pipeline we are building can be described as follows:

- Once the developer pushes the code on the application repository (GitHub in our case), Jenkins is notified
of the update
- Jenkins builds and tests the application using Maven
- Once the application has been built, we'll analyze it using a static code analysis tool called Sonarqube to help us identify any potential security issues and help the developer remediate before the code is pushed into production
- When successful, we build and push a docker image into our dockerhub registry
- We also scan the docker image with Trivy by doing a static analysis to identify any additional vulnerabilities that may reside within the container
- We then update a set of k8s manifest files which reside in a separate repository (devops)
- ArgoCD will monitoring the devops repository for any changes. As soon as a change is detected, it will deploy the latest version of the tag into our K8s cluster
- Then a Slack notification is sent if the deployment and build is successful, also if there's been an issue in the build steps along the way.

We'll follow a declarative pipeline in Jenkins

## 1. Install Jenkins

We're going to use a separate Jenkins server running Ubuntu Linux 22.04
The installation is described in the [Jenkins documentation](https://www.jenkins.io/doc/book/installing/linux/)

## Install Java

Jenkins requires Java in order to run.
There Java implementations we can use. OpenJDK is the most popular and this is what we'll be using.

```
sudo apt update
sudo apt install openjdk-11-jre
java -version
```

## Install Jenkins (LTS release)

```
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

## Start Jenkins 

- Enable Jenkins

`sudo systemctl enable jenkins`

- Start Jenkins

`sudo systemctl start jenkins`

- Check status

`sudo systemctl status jenkins`

Next, we need to install nginx and set up reverse proxy to access jenkins server by domain name

## Update Package Repository and Upgrade

```
sudo apt update
sudo apt upgrade
```

## Installing Nginx

sudo apt install nginx

## Check that nginx is running

sudo systemctl status nginx

## Check that the web server is running

Go to `http://jenkins_server_ip_address`

## Configure Nginx to point to Jenkins through Reverse Proxy

### Create Configuration File

`sudo vi /etc/nginx/sites-available/jenkins`

We will be using a local DNS server because our solution is tested in a local environment. 
We don't want to be using IP addresses, so we'll set up our own DNS server.

Now let's set up the local DNS server

We're going to configure Dnsmasq dns server on an Ubuntu server 22.04 box

Make sure the IP address is statically set.





