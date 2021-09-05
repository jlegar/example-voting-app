# Build image from Maven
FROM maven:3.6.1-jdk-8-alpine

# Change working directory
WORKDIR /app

# Copy app data
COPY ./target/worker-jar-with-dependencies.jar ./worker-app.jar

# Run command to start app
CMD java -jar worker-app.jar

