### STATIC LAYER
FROM maven:3.9.6-eclipse-temurin-21-alpine as builder
LABEL MAINTAINER="Glenn ten Cate <glenn.ten.cate@gmail.com>"


### DYNAMIC LAYER
FROM builder
copy Database.db /skf/
COPY src/ /skf/src/
COPY pom.xml /skf/

### CREATING LIMITED USER AND CHAING WORKDIR FOLDER OWNERSHIP
RUN addgroup -S spring && adduser -S spring -G spring && chown -R spring:spring /skf

### SWITCHING TO THE LIMITED USER
USER spring:spring
WORKDIR /skf
RUN mvn package -Dmaven.test.skip=true

ENTRYPOINT ["java","-jar","/skf/target/app.jar"]
