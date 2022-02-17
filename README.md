## Springboot - Build container image using custom Base OS / Buildpack

### Pre-requisite

* Docker CLI 
* Local Docker daemon should be running
* Java 11
* Maven
* Access to Internet
* Access to Docker Registry (Habor used here)

### Build, Create Image, Tag and Push to Container Registry

#### Change `pom.xml`

* Include container registry credentials
* Refer [here](https://docs.spring.io/spring-boot/docs/current/maven-plugin/reference/htmlsingle/#build-image.
  customization) for Image customizations, like custom image tag

```xml
   <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <image>
                        <name>${registry.projectname}/${project.artifactId}:${project.version}</name>
<!--                    <publish>true</publish>-->
                    </image>
                    <docker>
                        <publishRegistry>
                            <username>${registry.user}</username>
                            <password>${registry.password}</password>
                            <url>${registry.uri}</url>
                        </publishRegistry>
                    </docker>
                </configuration>
            </plugin>
        </plugins>
    </build>
```


```
./mvnw spring-boot:build-image \
    -Dregistry.projectname=$REGISTRY_PROJECT_NAME  \
    -Dregistry.user=$REGISTRY_USER \
    -Dregistry.password=$REGISTRY_PASSWORD \
    -Dregistry.uri=$REGISTRY_URI \
    -Dspring-boot.build-image.publish=true
```


## To build stack

```
cd docker

docker build . -t ${REGISTRY_PROJECT_NAME}/cnbs-sample-stack-base:bionic --target base
docker build . -t ${REGISTRY_PROJECT_NAME}/cnbs-sample-stack-build:bionic --target build
docker build . -t ${REGISTRY_PROJECT_NAME}/cnbs-sample-stack-run:bionic --target run
```

## Builder

pack builder create ${REGISTRY_PROJECT_NAME}/java8-builder:bionic --config ./cnb/builder/bionic/builder.toml
ERROR: invalid run image config: failed to fetch image: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
rsatya-a01:spring-hello Sranjan$ pack builder create ${REGISTRY_PROJECT_NAME}/java8-builder:bionic --config ./cnb/builder/bionic/builder.toml
bionic: Pulling from apjappnav/cnbs-sample-stack-run
Digest: sha256:6e1ea1062f620af8c56bc44dd44d3a6b2990120b8a9492f1afe428e1a7857efd
Status: Image is up to date for ${REGISTRY_PROJECT_NAME}/cnbs-sample-stack-run:bionic
bionic: Pulling from apjappnav/cnbs-sample-stack-build
Digest: sha256:aa929294363f933a155bfaf3e79586ee86338d284fd3cba094cc96cdd21450e1
Status: Image is up to date for ${REGISTRY_PROJECT_NAME}/cnbs-sample-stack-build:bionic
Downloading from https://github.com/buildpacks/lifecycle/releases/download/v0.13.3/lifecycle-v0.13.3+linux.x86-64.tgz
5.25 MB/5.25 MB
Successfully created builder image ${REGISTRY_PROJECT_NAME}/java8-builder:bionic
Tip: Run pack build <image-name> --builder ${REGISTRY_PROJECT_NAME}/java8-builder:bionic to use this builder


## Buildpack
<builder>gcr.io/paketo-buildpacks/builder:base</builder>
<buildpacks>
<buildpack>gcr.io/paketo-buildpacks/amazon-corretto:latest</buildpack>
<buildpack>gcr.io/paketo-buildpacks/maven:latest</buildpack>
<buildpack>gcr.io/paketo-buildpacks/executable-jar:latest</buildpack>
<buildpack>gcr.io/paketo-buildpacks/spring-boot:latest</buildpack>
</buildpacks>

## Test builder
pack build hello-world --builder ${REGISTRY_PROJECT_NAME}/java8-builder:bionic --path .