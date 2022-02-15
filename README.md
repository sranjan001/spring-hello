## Build Image (With default BaseOS)
```
/mvnw spring-boot:build-image
```
## Image customization (spring-boot:build-image)

https://docs.spring.io/spring-boot/docs/current/maven-plugin/reference/htmlsingle/#build-image.customization

./mvnw spring-boot:build-image -Dregistry_project_name=<project_name> -Dregistry_user='<registry_user>' -Dregistry_password=<registry_password> -Dregistry_uri=<registry_uri> -Dspring-boot.build-image.publish=true