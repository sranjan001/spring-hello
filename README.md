## Springboot - Build container image and deploy to Kubernetes

### Pre-requisite

* Docker CLI 
* Local Docker daemon should be running
* Java 11
* Maven
* Access to Internet
* Access to Docker Registry (Habor used here)

### Build and Create Image 
Uses default BaseOS

```
./mvnw spring-boot:build-image
```

* Build application and create JAR
* Build image
  * Pull builder image 'docker.io/paketobuildpacks/builder:base'
  * Pull run image 'docker.io/paketobuildpacks/run:base-cnb
  * Execute lifecycle version v0.13.3
  * Using build cache volume
  * Run creator
    * DETECT
    * ANALYZE
    * RESTORE
    * BUILD
      * Paketo CA Certificates Buildpack 3.0.3 
      * Paketo BellSoft Liberica Buildpack 9.1.0
      * BellSoft Liberica JRE 11.0.14
      * Paketo Syft Buildpack 1.7.0
      * Paketo Executable JAR Buildpack 6.0.4
      * Paketo Spring Boot Buildpack 5.5.0
      * Spring Cloud Bindings 1.8.1
    * EXPORT
      * Add layer
      * Add label
      * Set default process type
BUILD SUCCESS

Image created with name from `pom.xml`

```
<artifactId>spring-hello</artifactId>
<version>0.0.2-SNAPSHOT</version>
```

Final image name: `spring-hello:0.0.2-SNAPSHOT`

### Tag and Push to Container Registry

```
docker login REGISTRY-URI
docker tag spring-hello:0.0.2-SNAPSHOT REGISTRY-PROJECT-NAME/spring-hello:latest
docker push REGISTRY-PROJECT-NAME/spring-hello:latest

## For Harbor, REGISTRY-PROJECT-NAME = REGISTRY-URI/PROJECT-NAME
```

### Prepare Kubernetes

#### Create secret for container registry

* Create Namespace `dev`
* Create registry-secret. Refer [Add ImagePullSecrets to a service account](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account)
* Create Service Account `deploy-robot` and bind to the secret
* Create Deployment include Service Account name to pod spec
* Create NodePort Service and expose the deployment

Refer [deploy.yaml](k8s/deploy.yaml)
```
kubectl apply -f k8s/deploy.yaml
```

* Get external IP of any worker node

```
kubectl get nodes -o wide
```

* Then test using 

`curl EXTERNAL-IP:30009`