# First stage: build
FROM openjdk:11 AS build
WORKDIR /usr/src
COPY Main.java .
RUN javac Main.java

# Second stage: run
FROM openjdk:11-jre
WORKDIR /app
COPY --from=build /usr/src/Main.class .
CMD ["java", "Main"]
