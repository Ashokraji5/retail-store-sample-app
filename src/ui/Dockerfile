# =========================
# Build Stage
# =========================

FROM public.ecr.aws/amazonlinux/amazonlinux:2023 AS build

RUN dnf install -y \
    java-21-amazon-corretto-headless \
    maven \
    which \
    tar \
    gzip \
    && dnf clean all

WORKDIR /app

COPY .mvn .mvn
COPY mvnw .
COPY pom.xml .

RUN ./mvnw dependency:go-offline -B

COPY src ./src

RUN ./mvnw -DskipTests package

# =========================
# Package Stage
# =========================

FROM public.ecr.aws/amazonlinux/amazonlinux:2023

RUN dnf install -y \
    java-21-amazon-corretto-headless \
    && dnf clean all

WORKDIR /app

COPY --from=build /app/target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
