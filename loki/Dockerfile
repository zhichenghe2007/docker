FROM grafana/loki:2.2.1

MAINTAINER kennylee <kennylee26@gmail.com>

USER root

# 使用阿里云镜像
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories

# Install base packages
RUN apk --no-cache update && \
    apk --no-cache upgrade && \
    apk --no-cache add curl bash tzdata tar unzip && \
    ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
    echo "Asia/Shanghai" > /etc/timezone && \
    echo -ne "Alpine Linux 3.9.6 image. (`uname -rsv`)\n" >> /root/.built && \
    rm -fr /tmp/* /var/cache/apk/*

USER loki

EXPOSE 3100

ENTRYPOINT ["/usr/bin/loki"]

CMD ["-config.file=/etc/loki/local-config.yaml"]
