FROM registry.cn-hangzhou.aliyuncs.com/kennylee/alpine:3.4

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN addgroup -S redis && adduser -S -G redis redis

# grab su-exec for easy step-down from root
RUN apk add --no-cache 'su-exec>=0.2'

ENV REDIS_VERSION 3.2.13
ENV REDIS_DOWNLOAD_URL http://download.redis.io/releases/redis-${REDIS_VERSION}.tar.gz
ENV REDIS_DOWNLOAD_SHA 862979c9853fdb1d275d9eb9077f34621596fec1843e3e7f2e2f09ce09a387ba

# for redis-sentinel see: http://redis.io/topics/sentinel
RUN set -ex \
	\
	&& apk add --no-cache --virtual .build-deps \
		gcc \
		linux-headers \
		make \
		musl-dev \
		tar \
	\
	&& wget -O redis.tar.gz "$REDIS_DOWNLOAD_URL" \
	&& echo "$REDIS_DOWNLOAD_SHA *redis.tar.gz" | sha256sum -c - \
	&& mkdir -p /usr/src/redis \
	&& tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
	&& rm redis.tar.gz \
	\
# Disable Redis protected mode [1] as it is unnecessary in context
# of Docker. Ports are not automatically exposed when running inside
# Docker, but rather explicitely by specifying -p / -P.
# [1] https://github.com/antirez/redis/commit/edd4d555df57dc84265fdfb4ef59a4678832f6da
	&& grep -q '^#define CONFIG_DEFAULT_PROTECTED_MODE 1$' /usr/src/redis/src/server.h \
	&& sed -ri 's!^(#define CONFIG_DEFAULT_PROTECTED_MODE) 1$!\1 0!' /usr/src/redis/src/server.h \
	&& grep -q '^#define CONFIG_DEFAULT_PROTECTED_MODE 0$' /usr/src/redis/src/server.h \
# for future reference, we modify this directly in the source instead of just supplying a default configuration flag because apparently "if you specify any argument to redis-server, [it assumes] you are going to specify everything"
# see also https://github.com/docker-library/redis/issues/4#issuecomment-50780840
# (more exactly, this makes sure the default behavior of "save on SIGTERM" stays functional by default)
	\
	&& make -C /usr/src/redis \
	&& make -C /usr/src/redis install \
	\
	&& rm -r /usr/src/redis \
	\
	&& apk del .build-deps

RUN mkdir /data && chown redis:redis /data
VOLUME /data
WORKDIR /data

COPY docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod +x /*.sh
ENTRYPOINT ["/docker-entrypoint.sh"]

EXPOSE 6379
CMD [ "redis-server" ]
