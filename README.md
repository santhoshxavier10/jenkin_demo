# Local Git-Jenkins-Docker Pipeline on Ubuntu

## Overview

This repository provides a guide and configuration files to set up a local Continuous Integration/Continuous Deployment (CI/CD) pipeline using Git, Jenkins, and Docker on an Ubuntu EC2 instance. The pipeline is designed to automatically build and deploy a Dockerized Python web application whenever changes are pushed to the associated Git repository.

## Prerequisites

1. **EC2 Instance**: Create an Ubuntu EC2 instance on AWS.

2. **Docker Installation**: Install Docker on the Ubuntu instance using the provided script.

    ```bash
    curl -fsSL https://get.docker.com -o get-docker.sh
    sh get-docker.sh
    ```

3. **Java Installation**: Install OpenJDK 17 (Java Runtime Environment).

    ```bash
    sudo apt install openjdk-17-jre
    ```

4. **Jenkins Installation**: Set up Jenkins on the instance.

    ```bash
    # Import Jenkins Repository Key
    curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null

    # Add Jenkins Repository to Sources List
    echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

    # Update and Install Jenkins
    sudo apt-get update
    sudo apt-get install jenkins
    ```

5. **Firewall Configuration**: Allow traffic on port 8080 for Jenkins, SSH connections, and port 8000 for container communication.

    ```bash
    sudo ufw allow 8080
    sudo ufw allow OpenSSH
    sudo ufw allow 8000
    sudo ufw enable
    ```

6. **Jenkins Configuration**:
    - Start Jenkins service and check its status.
    - Retrieve the initialAdminPassword for Jenkins setup.

    ```bash
    sudo systemctl start jenkins.service
    sudo systemctl status jenkins
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

7. **Docker Hub Account**: Create a Docker Hub account and login to Docker on the Ubuntu instance.

    ```bash
    sudo docker login -u <username> -p <password>
    ```

8. **Jenkins-Docker Integration**: Add the Jenkins user to the docker group for Docker access.

    ```bash
    sudo usermod -aG docker jenkins
    sudo systemctl restart jenkins
    ```

## Jenkins Setup

1. Open `<Public_IP_Address_of_EC2_Instance>:8080` in a browser.

2. Log in with the initialAdminPassword obtained earlier.

3. Install suggested plugins and set up a new username and password.

4. Setup credentials in Jenkins, including Docker Hub username and password.

5. Make necessary changes in the Jenkinsfile, setting credentials id and Docker username.

## Local Development

1. Create a pipeline in Jenkins, specifying the GitHub repository and branch.

2. Build the pipeline to execute the defined stages.

3. Open `<Public_IP_Address_of_EC2_Instance>:8000` in a browser to view the application.

## Making Changes

To see the pipeline in action, make changes to the `hello.html` file in the `templates` folder of the repository. Commit the changes, and within a minute, the updates should reflect on the end page.
