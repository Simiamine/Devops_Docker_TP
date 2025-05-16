#build
FROM eclipse-temurin:21-jdk-alpine AS builder
WORKDIR /app
COPY Main.java .
RUN javac Main.java

#run
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
COPY --from=builder /app/Main.class .
CMD ["java", "Main"]