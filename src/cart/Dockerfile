
FROM public.ecr.aws/amazonlinux/amazonlinux:2023

LABEL service="retail-cart"
LABEL practice="v1.3"

RUN dnf --setopt=install_weak_deps=False install -y \
        java-21-amazon-corretto-headless \
    && dnf clean all \
    && rm -rf /var/cache/dnf

ENV JAVA_HOME=/usr/lib/jvm/java-21-amazon-corretto
ENV PATH="${JAVA_HOME}/bin:${PATH}"
ENV SPRING_PROFILES_ACTIVE=prod

WORKDIR /app

ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} /app/app.jar

EXPOSE 8080

ENTRYPOINT ["sh", "-c", "exec java $JAVA_OPTS -jar /app/app.jar"]
