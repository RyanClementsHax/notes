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
1. Instal Docker plugin
   1. From the dashboard, go to `Manage Jenkins > Manage Plugins > Available`
   2. Select the `Docker` plugin
   3. Click `Install without restart`
2. Setup Docker plugin
   1. From the dashboard, go to `Manage Jenkins > Manage Nodes and Clouds > Configure Clouds`
   2. Add a new Docker cloud
   3. Enter `docker` for the name
   4. Click `Docker Cloud details...` so we can configure how jenkins connects with docker
   5. Set the `Docker Host URI` to the docker host you want to use
   6. Click `Test Connection`
      - If the connection fails, make sure the docker host is running and you entered in the right uri
   7. Check the `Enabled` checkbox
      - If you don't do this, jenkins won't use this cloud at all, and you will cry when your pipelines break when they can't find any docker nodes to run on
   8. Click `Docker Agent templates...` so we can configure a build agent that has docker installed on it
   9. Enter `docker-agent` for the label
   10. Check the `Enabled` checkbox
       - If you don't do this, jenkins won't use this cloud at all, and you will cry when your pipelines break when they can't find any docker nodes to run on
   11. Enter `docker-agent` for the name
   12. Enter an image that has a jenkins build agent with docker installed on it for the docker image
       - [ghcr.io/felipecrs/jenkins-agent-dind](https://github.com/felipecrs/jenkins-agent-dind) is one open source option
   13. Click `Container settings...`
   14. Configure the docker agent image as needed
       - For `ghcr.io/felipecrs/jenkins-agent-dind`, do the following
           1.  Check the `Run container privileged` checkbox
           2.  Enter `/home/jenkins/agent` for the remote file system root
           3.  Choose `Attach Docker container` for `Connect method`
           4.  Enter `jenkins` as the user
           5.  Check the `Remote volumes` checkbox
   1.  Make sure you checked the `Enabled` checkbox for both the docker cloud and agent template
   2.  Click save

### Pipeline setup
1. Create the pipeline project
2. Configure pipeline with build agent
   1. From the dashboard, go to `Configure System`
   2. Scroll down to `Declarative Pipeline (Docker)`
   3. Enter `docker-agent` for the docker label

## Multibranch pipelines
- [doesn't support multiple repos](https://issues.jenkins.io/browse/JENKINS-62082?jql=labels%20%3D%20multibranch)
  - you may want other data or code to be kept in a separate repo, but this screws up jenkins's ability to do checkouts properly