
# =========================
# Build Stage
# =========================
FROM public.ecr.aws/amazonlinux/amazonlinux:2023 AS build-env

RUN dnf --setopt=install_weak_deps=False install -y \
        maven \
        java-21-amazon-corretto-headless \
        tar \
        gzip \
    && dnf clean all \
    && rm -rf /var/cache/dnf

ENV JAVA_HOME=/usr/lib/jvm/java-21-amazon-corretto
ENV PATH="${JAVA_HOME}/bin:${PATH}"

WORKDIR /build

COPY .mvn/ .mvn/
COPY mvnw pom.xml ./

RUN chmod +x mvnw \
    && ./mvnw -B -q dependency:go-offline

COPY src/ src/

RUN ./mvnw -B -DskipTests package -q \
    && set -eu \
    && JAR="$(find target -maxdepth 1 -type f -name '*.jar' -print -quit)" \
    && test -n "$JAR" \
    && cp "$JAR" /app.jar


# =========================
# Runtime Stage
# =========================
FROM public.ecr.aws/amazonlinux/amazonlinux:2023

LABEL service="retail-cart"
LABEL practice="v1.1"

RUN dnf --setopt=install_weak_deps=False install -y \
        java-21-amazon-corretto-headless \
        shadow-utils \
    && dnf clean all \
    && rm -rf /var/cache/dnf

ENV APPUSER=appuser
ENV APPUID=1000
ENV APPGID=1000

RUN groupadd --gid "${APPGID}" "${APPUSER}" \
    && useradd \
        --uid "${APPUID}" \
        --gid "${APPGID}" \
        --create-home \
        --home-dir /app \
        --shell /sbin/nologin \
        "${APPUSER}"

ENV JAVA_HOME=/usr/lib/jvm/java-21-amazon-corretto
ENV PATH="${JAVA_HOME}/bin:${PATH}"
ENV SPRING_PROFILES_ACTIVE=prod

WORKDIR /app

COPY --from=build-env --chown=appuser:appuser /app.jar /app/app.jar

USER appuser

EXPOSE 8080

ENTRYPOINT ["sh", "-c", "exec java $JAVA_OPTS -jar /app/app.jar"]
