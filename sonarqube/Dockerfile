# Build properties
ARG ALPINE_VERSION=3.17
ARG SONARQUBE_VERSION=9.9.0.65466
ARG MSR=https://hub.docker.com/
ARG MSRCREDS=dockerhub-creds
ARG JAVA_VERSION=17
ARG SONARQUBE_DOWNLOAD_URL=https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-${SONARQUBE_VERSION}.zip

# Use the official Eclipse Temurin image as the base image
FROM eclipse-temurin:${JAVA_VERSION}-jre AS build

# Set environment variables
ENV SONARQUBE_HOME=/opt/sonarqube \
    SONARQUBE_VERSION=${SONARQUBE_VERSION}

# Install necessary packages
RUN apk update && \
    apk add --no-cache curl unzip && \
    rm -rf /var/lib/apt/lists/*

# Download and extract SonarQube
RUN curl -L -o sonarqube.zip ${SONARQUBE_DOWNLOAD_URL} \
    && unzip sonarqube.zip \
    && rm sonarqube.zip \
    && mv sonarqube-${SONARQUBE_VERSION} sonarqube

# Create user and group for SonarQube
RUN addgroup -S -g 1000 sonarqube && \
    adduser -S -u 1000 -G sonarqube sonarqube

# Copy sonar.properties
COPY sonar.properties ${SONARQUBE_HOME}/conf/sonar.properties

# Set environment variables for JDBC and other configurations
ENV SONAR_HOST_URL= \
    SONAR_JDBC_URL= \
    SONAR_JDBC_USERNAME= \
    SONAR_JDBC_PASSWORD= \
    SONAR_SECURITY_REALM= \
    SONAR_SECURITY_SAVEPASSWORD= \
    SONAR_TYPESCRIPT_LCOV_REPORTPATHS=

# Expose SonarQube's default port
EXPOSE 9000

# Set the work directory
WORKDIR ${SONARQUBE_HOME}

# Change ownership of the SonarQube folder
RUN chown -R sonarqube:sonarqube ${SONARQUBE_HOME}

# Run as the sonarqube user
USER sonarqube

# Set the entrypoint
ENTRYPOINT ["./bin/linux-x86-64/sonar.sh", "start"]
