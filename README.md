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

## Buildpack
