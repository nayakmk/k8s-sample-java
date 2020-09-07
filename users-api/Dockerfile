# Start with a base image containing Java runtime (mine java 8)
FROM adoptopenjdk/openjdk8:alpine-slim

# Add Maintainer Info
LABEL maintainer="creekworm@gmail.com"

# Add a volume pointing to /tmp
VOLUME /tmp

# Make port 8080 available to the world outside this container
EXPOSE 8080

# The application's jar file (when packaged)
ARG JAR_FILE=build/libs/users-api-0.0.1-SNAPSHOT.jar

# Add the application's jar to the container
ADD ${JAR_FILE} users-api.jar

# Run the jar file 
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/users-api.jar"]