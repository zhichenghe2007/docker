FROM registry.cn-hangzhou.aliyuncs.com/kennylee/tomcat:tomcat8-jre7

MAINTAINER kennylee <kennylee26@gmail.com>

# Install.
RUN \
  apt-get update && \
  apt-get install -y openssh-server && \
  apt-get install -y inetutils-ping net-tools dnsutils supervisor && \
  rm -rf /var/lib/apt/lists/*

RUN mkdir /var/run/sshd

# Add root password
RUN echo 'root:111111' | chpasswd

RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
# old version sshd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile
RUN ln -s ${CATALINA_HOME} ${HOME}/tomcat

# set ssh user source
RUN echo "export JAVA_HOME=${JAVA_HOME}" >> ${HOME}/.bashrc
RUN echo "export JAVA_OPTS=\"${JAVA_OPTS}\"" >> ${HOME}/.bashrc
RUN echo "export CATALINA_HOME=${CATALINA_HOME}" >> ${HOME}/.bashrc

RUN sed -i "s@\/usr\/lib\/jvm\/java-7-oracle@${JAVA_HOME}@" /etc/init.d/tomcat
RUN sed -i "s@-server -Xms128m -Xmx1024m -XX\:PermSize=64M -XX\:MaxPermSize=192M@${JAVA_OPTS}@" /etc/init.d/tomcat

# Define working directory.
WORKDIR ${HOME}

# Define default command.
EXPOSE 22
EXPOSE 8080

COPY supervisord_wrapper.sh /supervisord_wrapper.sh
RUN chmod +x /*.sh

COPY supervisor/supervisord.conf /etc/supervisor/supervisord.conf
COPY supervisor/conf.d/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Define default command.
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]
