# syntax=docker/dockerfile:1.7

FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /build
COPY pom.xml ./
RUN mvn -B -q dependency:go-offline
COPY src src
RUN mvn -B -q -DskipTests package \
 && mv target/*.jar target/app.jar

FROM eclipse-temurin:17-jre-alpine AS runtime
RUN apk add --no-cache wget \
 && addgroup -S spring \
 && adduser -S -G spring spring
WORKDIR /app
COPY --from=builder --chown=spring:spring /build/target/app.jar app.jar
USER spring
EXPOSE 8082
HEALTHCHECK --interval=20s --timeout=5s --start-period=40s --retries=5 \
  CMD wget -qO- http://localhost:8082/swagger-ui.html >/dev/null 2>&1 || exit 1
ENTRYPOINT ["java","-jar","/app/app.jar"]
