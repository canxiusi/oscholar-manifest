# syntax=docker/dockerfile:1
FROM maven:3.9-eclipse-temurin-17 AS build

ARG GITHUB_TOKEN
ARG GITHUB_ACTOR=canxiusi

WORKDIR /build

RUN mkdir -p /root/.m2 && echo '<settings>\
  <servers>\
    <server>\
      <id>github</id>\
      <username>'${GITHUB_ACTOR}'</username>\
      <password>'${GITHUB_TOKEN}'</password>\
    </server>\
  </servers>\
  <profiles>\
    <profile>\
      <id>github</id>\
      <repositories>\
        <repository>\
          <id>github</id>\
          <url>https://maven.pkg.github.com/canxiusi/oscholar-common</url>\
        </repository>\
      </repositories>\
    </profile>\
  </profiles>\
  <activeProfiles>\
    <activeProfile>github</activeProfile>\
  </activeProfiles>\
</settings>' > /root/.m2/settings.xml

COPY pom.xml .
COPY oscholar-social-api/pom.xml oscholar-social-api/
COPY oscholar-social-service/pom.xml oscholar-social-service/
RUN --mount=type=cache,target=/root/.m2/repository mvn dependency:go-offline -s /root/.m2/settings.xml -B
COPY oscholar-social-api/src oscholar-social-api/src
COPY oscholar-social-service/src oscholar-social-service/src
RUN --mount=type=cache,target=/root/.m2/repository mvn package -s /root/.m2/settings.xml -DskipTests -B

FROM dragonwell-registry.cn-hangzhou.cr.aliyuncs.com/dragonwell/dragonwell:17

WORKDIR /app
COPY --from=build /build/oscholar-social-service/target/oscholar-social-service-*.jar app.jar

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=3s \
  CMD wget -qO- http://localhost:8080/actuator/health || exit 1

ENV ACTIVE=dev

ENTRYPOINT ["java", "-jar", "app.jar"]
