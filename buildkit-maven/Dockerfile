ARG APP_DIR=simple-java-maven-app

# Build container
FROM maven:3.8.7-eclipse-temurin-11 AS build
ARG APP_DIR
ARG OTEL_EXPORTER_OTLP_ENDPOINT
WORKDIR /
COPY ${APP_DIR}.git /${APP_DIR}.git
RUN git clone /${APP_DIR}.git
RUN cd ${APP_DIR} \
        && ulimit -n 10000 \
	&& OTEL_EXPORTER_OTLP_TRACES_ENDPOINT=${OTEL_EXPORTER_OTLP_ENDPOINT} mvn -B -DskipTests clean validate compile package \
	&& OTEL_EXPORTER_OTLP_TRACES_ENDPOINT=${OTEL_EXPORTER_OTLP_ENDPOINT} mvn test

# Release image
FROM gcr.io/distroless/java:11
ARG APP_DIR
# TODO outsources this to a CI
ENV APP_DIR=${APP_DIR}
ENV APP_VERSION=1.0-SNAPSHOT
ENV APP_NAME=my-app
COPY --from=build /${APP_DIR}/target/* /${APP_DIR}
COPY ./entrypoint.sh /
WORKDIR /
# ENTRYPOINT ["java", "-jar", "/$APP_DIR/$APP_NAME-$APP_VERSION.jre"]
ENTRYPOINT ["./entrypoint.sh"]
