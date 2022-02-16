## Springboot - Build container image and push to container registry using Maven configuration

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
  * Push image
     BUILD SUCCESS

Image created with name from `pom.xml`

```
<artifactId>spring-hello</artifactId>
<version>0.0.3-SNAPSHOT</version>
```

Final image name: `spring-hello:0.0.3-SNAPSHOT`
