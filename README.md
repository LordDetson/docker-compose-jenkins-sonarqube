# docker-compose-jenkins-sonarqube

## Introduction
This repository provides `docker-compose.yml` sample file to run Jenkins and SonarQube in Docker.
It also provides instructions for connecting SonarQube to Jenkins

## Assumptions
- You have Docker and Docker-Compose installed (Docker for Mac, Docker for Windows, get.docker.com and manual Compose installed for Linux).
- You want to use Docker for local development (i.e. never need to install php or npm on host) and have dev and prod Docker images be as close as possible.
- You don't want to lose fidelity in your dev workflow. You want a easy environment setup, using local editors, debug/inspect, local code repo, while web server runs in a container.
- You use docker-compose for local development only (docker-compose was never intended to be a production deployment tool anyway).
- The docker-compose.yml is not meant for docker stack deploy in Docker Swarm, it's meant for happy local development.
- The project must have the **jacoco** plugin.

## Setup steps
1. Make sure the image tags in the file are appropriate for you.
2. Make sure you are using the correct volume mount path on line 8:
```
- {volume_mount_path/named_volume}:/var/jenkins_home
```
Note:
	Instead, you can use named volume that will not be linked to a directory on the local machine.
	But then you need to add a new named volume to the Volume section at the bottom of the file.
	
3.	Running `docker-compose up -d` is all you need.

4. Install Maven
   1. Go to Manage Jenkins > Global Tool Configuration > Maven
   2. Click "Maven installations"
   3. Write the name (For example: `Maven`)
   4. Select Install automatically
   5. Select the vesion (For example: `3.6.3`)

5. Setup Java
   1. Go to Manage Jenkins > Global Tool Configuration > JDK
   2. Click "JDK installations"
   3. Write the name (For example: `JDK`)
   4. Write the JAVA_HOME - `/opt/java/openjdk`

6. Install SonarQube Scanner plugin
   1. Go to Manage Jenkins > Manage Plugins > Available
   2. Type "SonarQube Scanner for Jenkins" in the search bar
   3. Select found plugin
   4. Click "Download now and install after restart"
   5. Select "Restart Jenkins when installation is complete and no jobs are running"
   6. Wait for the plugin to download and install, then the server will restart

7. Setup SonarQube Scanner
   1. Open and login SonarQube
   2. Go to Administration > Security > Users
   3. Click "Update Tokens"
   4. Enter token name
   5. Click "Generate"
   6. Copy the token
   7. Open Jenkins
   8. Go to Manage Jenkins > Manage Credentials
   9. Click "Jenkins" link
   10. Click "Global credentials (unrestricted)" link
   11. Click "Add Credentials"
   12. Select "Secret text"
   13. Paste Token to the Secret field
   14. Enter ID (For example: `SonarQube`)
   15. Click "Create"
   16. Go to Manage Jenkins > Configure System > SonarQube Servers
   17. Select "Enable injection of SonarQube server configuration as build environment variables"
   18. Click add SonarQube
   19. Enter the name (For example: `SonarQube`)
   20. Enter the Server URL - `http://sonarqube:9000`
   21. Select Server authentication token what we created earlier

8. Create Job
   1. Click "New Item"
   2. Enter the name (For example: `first-project`)
   3. Select "Freestyle project"
   4. Go to Source Code Management
   5. Select Git
   6. Enter Repository URL
   7. Enter Enter required branch to the Branches to build field
   8. Go to Build Environment
   9. Select Prepare SonarQube Scanner environment and Select Server authentication token
   10. Go to Build
   11. Click Add build step and select installed Maven
   12. Enter `clean test install $SONAR_MAVEN_GOAL` to the Goals field
   13. Click Save

9. Go to the created job
10. Click Build Now
11. Wait for the build to finish

After all these steps, you will be able to see the results of the SonarQube analysis.
