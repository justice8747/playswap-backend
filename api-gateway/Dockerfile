# Multi-stage production build for api-gateway
FROM maven:3.9.9-eclipse-temurin-21-alpine AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline -B || true
COPY src ./src
RUN mvn clean package -DskipTests -B

# Minimal runtime image
FROM eclipse-temurin:21-jre-alpine
RUN apk add --no-cache curl
WORKDIR /app
RUN addgroup -S playswap && adduser -S playswap -G playswap
COPY --from=builder /app/target/*.jar app.jar
RUN chown -R playswap:playswap /app
USER playswap

EXPOSE 9000
ENTRYPOINT ["java", "-XX:+UseContainerSupport", "-XX:MaxRAMPercentage=75.0", "-jar", "app.jar"]
