# CI/CD Pipeline with Jenkins, Docker, Ansible, and Tomcat

## Overview

This project demonstrates a CI/CD pipeline using **Jenkins**, **Docker**, **Ansible**, and **Tomcat**.  
The application is built with **Maven** inside a Docker container, then transferred and deployed to a remote **Tomcat** server using **Ansible**.

## Pipeline Stages

1. **Build**
   - Executed inside a Docker-based Jenkins agent.
   - Maven builds the project and generates a `.war` file inside the `target/` directory.
   - Required files are `stash`ed for use on another agent.

2. **Unstash on Jenkins Master**
   - Files are `unstash`ed on the Jenkins master (or controller).
   - The `.war` file and Ansible scripts become available for deployment.

3. **Deployment via Ansible**
   - The WAR file is copied to the remote server.
   - Ansible runs a playbook that stops Tomcat, deploys the new WAR, and restarts the service.
