# Jenkins

## Running in docker container
1. start the container
    ```bash
    docker run -p 8080:8080 -p 50000:50000 -d -v jenkins_home:/var/jenkins_home jenkins/jenkins:latest
    ```
2. get the password from the logs
3. open jenkins in the browser and finish initializing jenkins with that password
4. all set!

## Running in docker compose
```yml
version: '3.8'

services:
  socat:
    container_name: jenkins-docker
    image: alpine/socat
    restart: unless-stopped
    networks:
      jenkins-network:
        aliases:
          - docker
    expose:
      - "2375"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins:latest
    restart: unless-stopped
    networks:
      - jenkins-network
    ports:
      - 8080:8080
      - 50000:50000
    volumes:
      - data:/var/jenkins_home
    environment:
      - DOCKER_HOST=tcp://docker:2375
      - DOCKER_TLS_VERIFY=""

networks:
  jenkins-network:
    name: jenkins

volumes:
  data:
    name: jenkins_home
```
- The `socat` container, `jenkins-network`, and docker environment variables are needed if you want jenkins to use the host's docker for build agents or for those agents to build docker images
- The uri for the docker host to use when configuring docker in jenkins is `tcp://docker:2375` in this case

## Setup docker in docker build agents
- some of these resources may be useful
  - [installing docker](https://www.jenkins.io/doc/book/installing/docker/)
  - [build a multibranch pipeline project](https://www.jenkins.io/doc/tutorials/build-a-multibranch-pipeline-project/)

### Plugin setup
1. instal Docker plugin
   1. from the dashboard, go to `Manage Jenkins > Manage Plugins > Available`
   2. select the `Docker` plugin
   3. click `Install without restart`
2. setup Docker plugin
   1. from the dashboard, go to `Manage Jenkins > Manage Nodes and Clouds > Configure Clouds`
   2. add a new Docker cloud
   3. enter `docker` for the name
   4. click `Docker Cloud details...` so we can configure how jenkins connects with docker
   5. set the `Docker Host URI` to the docker host you want to use
   6. click `Test Connection`
      - If the connection fails, make sure the docker host is running and you entered in the right uri
   7. check the `Enabled` checkbox
      - If you don't do this, jenkins won't use this cloud at all, and you will cry when your pipelines break when they can't find any docker nodes to run on
   8. click `Docker Agent templates...` so we can configure a build agent that has docker installed on it
   9. enter `docker-agent` for the label
   10. check the `Enabled` checkbox
       - If you don't do this, jenkins won't use this cloud at all, and you will cry when your pipelines break when they can't find any docker nodes to run on
   11. enter `docker-agent` for the name
   12. enter an image that has a jenkins build agent with docker installed on it for the docker image
       - [felipecrs/jenkins-agent-dind](https://github.com/felipecrs/jenkins-agent-dind) is one open source option
   13. click `Container settings...`
   14. enter the network you want the container to be on for `Network`
      - For local, containerized development, this will be the docker network configured in your compose file (e.g. `jenkins`)
   15. configure the `DOCKER_HOST` variable to be whatever you put in the `Docker Host URI` earlier
      - For local, containerized development, use `tcp://docker:2375`
      - Configuring this is how we can get the build agents to cache docker images by pointing it to the external docker host
   16. configure the docker agent image as needed
       - For `felipecrs/jenkins-agent-dind`, do the following
           1.  Check the `Run container privileged` checkbox
           2.  Enter `/home/jenkins/agent` for the remote file system root
           3.  Choose `Attach Docker container` for `Connect method`
           4.  Enter `jenkins` as the user
           5.  Check the `Remove volumes` checkbox
   17. make sure you checked the `Enabled` checkbox for both the docker cloud and agent template
   18. click save

### Pipeline setup
1. create the pipeline project
2. configure pipeline with build agent
   1. from the dashboard, go to `Configure System`
   2. scroll down to `Declarative Pipeline (Docker)`
   3. enter `docker-agent` for the docker label

## Multibranch pipelines
- [doesn't support multiple repos](https://issues.jenkins.io/browse/JENKINS-62082?jql=labels%20%3D%20multibranch)
  - you may want other data or code to be kept in a separate repo, but this screws up jenkins's ability to do checkouts properly

## Dynamic branch param
- when configuring the pipeline
   1. check the `This project is parameterized` checkbox
   2. enter `branch` for the name
   3. check `Trim the string` checkbox for good measure
   4. enter `*/${branch}` as the `Branch Specifier`
   5. uncheck `Lightweight checkout` if the pipeline is complaining about not being able to find the branch 