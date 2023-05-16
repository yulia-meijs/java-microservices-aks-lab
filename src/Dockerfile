# Set default value for args
ARG ARTIFACT_NAME=spring-petclinic-config-server
ARG APP_PORT=8080
ARG VERSION=2.7.6

# Use a base image with Java and Maven installed
FROM maven:3.8.3-openjdk-11 AS builder

# Set the working directory in the container
WORKDIR /app

# Copy the Maven project file
COPY pom.xml .

# Copy the application source code
COPY . .

# Build the application
RUN mvn package -DskipTests

# Use a lightweight base image with Java installed
FROM adoptopenjdk:11-jre-hotspot

# Get args to this build stage
ARG ARTIFACT_NAME
ARG APP_PORT
ARG VERSION

# Set the working directory in the container
WORKDIR /app

# Copy the application artifact from the builder stage

COPY --from=builder /app/${ARTIFACT_NAME}/target/${ARTIFACT_NAME}-${VERSION}.jar app.jar

# Get Application Insights jar
RUN  apt-get update \
  && apt-get install -y wget \
  && rm -rf /var/lib/apt/lists/*
RUN wget https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.4.3/applicationinsights-agent-3.4.3.jar
RUN mv applicationinsights-agent-3.4.3.jar ai.jar

# Expose the application port
EXPOSE ${APP_PORT} 8778 9779

# Run the jar file
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
