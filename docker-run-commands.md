# Create a network
```
docker network create jenkins
```
# Create your volume for Jenkins
mkdir /home/ubuntu/jenkins-data

### Start DinD container

```
docker run --name jenkins-docker --rm --detach \
  --privileged --network jenkins --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume /home/ubuntu/jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  docker:dind --storage-driver overlay2
```

# 3. Run Docker Build for Jenkins with DinD
```
docker build -t myjenkins-blueocean:2.492.1-1 .
```

# 4. Start Jenkins with DinD container
```
docker run --name jenkins-blueocean --restart=on-failure --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 --publish 50000:50000 \
  --volume /home/ubuntu/jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  myjenkins-blueocean:2.492.1-1
```

# Get your Jenkins Initial Password 
```
docker exec jenkins-blueocean cat /var/jenkins_home/secrets/initialAdminPassword
```