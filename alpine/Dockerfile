FROM adoptopenjdk/openjdk11:x86_64-alpine-jdk-11.0.7_10 as builder

MAINTAINER Stephane Martin stephane@stephanemartin.fr

ENV TOMCAT_MAJOR=9 \
    TOMCAT_VERSION=9.0.35

RUN apk add --no-cache  curl unzip

RUN addgroup -S jpetstore && adduser -S jpetstore -G jpetstore

USER jpetstore
WORKDIR /home/jpetstore
RUN curl -kL https://github.com/mybatis/jpetstore-6/releases/download/mybatis-jpetstore-6.0.1/mybatis-jpetstore-6.0.1-bundle.zip > mybatis-jpetstore-6.0.1-bundle.zip
RUN unzip mybatis-jpetstore-6.0.1-bundle.zip


RUN curl -kL http://archive.apache.org/dist/tomcat/tomcat-${TOMCAT_MAJOR}/v${TOMCAT_VERSION}/bin/apache-tomcat-${TOMCAT_VERSION}.tar.gz | tar -xz
RUN mv apache-tomcat-* tomcat \
	&& rm -rf tomcat/webapps/* \
	&& mv /home/jpetstore/mybatis-jpetstore-6.0.1/*.war tomcat/webapps/ROOT.war
RUN mkdir -p WEB-INF/classes/
COPY log4j.properties WEB-INF/classes/
RUN jar -uf tomcat/webapps/ROOT.war -C . /WEB-INF/classes/log4j.properties

FROM adoptopenjdk/openjdk11:x86_64-alpine-jre-11.0.7_10

ENV TOMCAT_HOME=/home/jpetstore/tomcat \
    CATALINA_HOME=/home/jpetstore/tomcat \
    CATALINA_OUT=/dev/null

RUN addgroup -S jpetstore && adduser -S jpetstore -G jpetstore
USER jpetstore
WORKDIR /home/jpetstore

COPY --chown=jpetstore:jpetstore --from=BUILDER ${TOMCAT_HOME} ${TOMCAT_HOME}

EXPOSE 8080
CMD "${TOMCAT_HOME}/bin/catalina.sh" "run"




