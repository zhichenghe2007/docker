FROM registry.cn-hangzhou.aliyuncs.com/kennylee/java:oracle-java8-python

# 维护者信息
MAINTAINER kennylee <kennylee26@gmail.com>

RUN apt-get update && \
    apt-get install -yq --no-install-recommends pwgen ca-certificates && \
    apt-get install -yq mysql-client libmysqlclient-dev && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN pip install MySQL-python beautifulsoup4

ENV TOMCAT_MAJOR_VERSION 8
ENV CATALINA_HOME /opt/tomcat

# INSTALL TOMCAT
RUN curl -sSL http://git.oschina.net/kennylee/python-tools/raw/master/tomcat_download.py | python - ${TOMCAT_MAJOR_VERSION}
RUN tar zxf apache-tomcat-*.tar.gz && \
    rm apache-tomcat-*.tar.gz && \
    mv apache-tomcat* ${CATALINA_HOME}

# Add service
COPY tomcat /etc/init.d/
RUN chmod +x /etc/init.d/tomcat

# Remove garbage
RUN rm -rf ${CATALINA_HOME}/webapps/examples && \
    rm -rf ${CATALINA_HOME}/webapps/docs && \
    rm -rf ${CATALINA_HOME}/webapps/ROOT

WORKDIR ${CATALINA_HOME}

COPY docker-entrypoint.py /entrypoint.py

EXPOSE 8080

CMD ["python", "/entrypoint.py"]
