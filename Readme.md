# Steps

## Pre-requisites

Docker Installed
Docker with Kubernetes Enabled

## Build Applications

### Create Dockerfile for each application

This file needs to be created at root of each module with name Dockerfile.

```yaml
# Start with a base image containing Java runtime (mine java 8)
FROM adoptopenjdk/openjdk8:alpine-slim

# Add Maintainer Info
LABEL maintainer="creekworm@gmail.com"

# Add a volume pointing to /tmp
VOLUME /tmp

# Make port 8080 available to the world outside this container
EXPOSE 9090

# The application's jar file (when packaged)
ARG JAR_FILE=build/libs/datareader-api-0.0.1-SNAPSHOT.jar

# Add the application's jar to the container
ADD ${JAR_FILE} datareader-api.jar

# Run the jar file 
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/datareader-api.jar"]
```

## Build Docker Images

docker build -t datareader-api:1.0 .
docker build -t users-api:1.0 .

### Run the Docker Image

docker run --rm -p 8080:9090 datareader-api:1.0

Verify if you are able to access the application via Docker. 

### Push the docker image to Docker Hub.

#### Change Tag to DockerHub Specific Repo

docker tag <image-id> creekworm/datareader-api
docker tag <image-id> creekworm/users-api

#### Change Tag to DockerHub Specific Repo

docker push creekworm/datareader-api
docker push creekworm/users-api

## Kubernetes Deployment

### Create the Yaml file

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: datareader
  labels:
    app: datareader
spec:
  containers:
    - name: datareader-api
      image: creekworm/datareader-api
```

### Deploy to Local Kubernetes as Pod

> kubectl apply -f datareader-api/kube/datareader-deployment.yaml
> kubectl apply -f kube/users-deployment.yaml

> kubectl get pods

### Expose a Service from Datareader Pod but only inside Cluster

> kubectl apply -f datareader-api/kube/datareader-service.yaml 

> kubectl get services

### Expose Services for External Access

> kubectl apply -f kube/users-service.yaml

### Describe a Service

> kubectl describe service user 

```
Name:                     user
Namespace:                default
Labels:                   <none>
Annotations:              Selector:  app=user
Type:                     LoadBalancer
IP:                       10.98.167.228
LoadBalancer Ingress:     localhost
Port:                     <unset>  80/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  30103/TCP
Endpoints:                10.1.0.172:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

Important Line: 

LoadBalancer Ingress:     localhost

### Access the User API

localhost:80 

80 -> User Pod (8080) -> DataReader Service (http://datareader.default.svc.cluster.local) 80 -> Data Reader ( 9090 ) 

## Reference

https://unofficial-kubernetes.readthedocs.io/en/latest/tasks/access-application-cluster/create-external-load-balancer/

https://medium.com/payscale-tech/imperative-vs-declarative-a-kubernetes-tutorial-4be66c5d8914

https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-networking-guide-beginners.html

https://kubeyaml.com/

https://www.mirantis.com/blog/introduction-to-yaml-creating-a-kubernetes-deployment/