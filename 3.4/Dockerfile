FROM alpine:3.6

ARG BUILD_DATE
ARG VCS_REF
ARG VERSION

LABEL \
    maintainer="robnightingale" \
    org.label-schema.build-date=$BUILD_DATE \
    org.label-schema.docker.dockerfile="/3.4/Dockerfile" \
    org.label-schema.license="Apache License 2.0" \
    org.label-schema.name="robnightingale/zookeeper" \
    org.label-schema.url="https://github.com/robnightingale" \
    org.label-schema.vcs-ref=$VCS_REF \
    org.label-schema.vcs-type="Git" \
    org.label-schema.vcs-url="https://github.com/robnightingale/docker-zookeeper"

ENV ZOOKEEPER_VERSION     $VERSION
ENV ZOOKEEPER_HOME        /usr/local/zookeeper-${ZOOKEEPER_VERSION}
ENV ZOOKEEPER_DATA_DIR    /zookeeper
ENV ZOOKEEPER_DATALOG_DIR /var/log/zookeeper
ENV ZOOKEEPER_CONF_DIR    ${ZOOKEEPER_HOME}/conf

ENV JAVA_HOME   /usr/lib/jvm/default-jvm
ENV PATH       $PATH:${JAVA_HOME}/bin:${ZOOKEEPER_HOME}/bin

RUN set -x \
    && apk update \
    && apk --no-cache add \
        bash \
        findutils \
        openjdk8-jre \
        su-exec \
        tar \
        wget \
    && apk --no-cache add --virtual .builddeps.1 \
        autoconf \
        automake \
        build-base \
        bzip2-dev \
        cmake \
        curl \
        fts-dev \
        fuse-dev \
        git \
        libexecinfo-dev \
        libtirpc-dev \
        libtool \
        maven \
        openjdk8 \
        snappy-dev \
        zlib-dev \
    && mirror_url=$( \
        wget -q -O - "http://www.apache.org/dyn/closer.cgi/?as_json=1" \
        | grep "preferred" \
        | sed -n 's#.*"\(http://*[^"]*\)".*#\1#p' \
        ) \
    && wget -q -O - ${mirror_url}/zookeeper/zookeeper-${ZOOKEEPER_VERSION}/zookeeper-${ZOOKEEPER_VERSION}.tar.gz \
        | tar -xzf - -C /usr/local \
    # user/dir/permission
    && adduser -D -g '' -s /sbin/nologin -u 1000 docker \
    && for user in zookeeper; do \
         adduser -D -g '' -s /sbin/nologin ${user}; \
       done \
    && mkdir -p \
        ${ZOOKEEPER_DATA_DIR} \
        ${ZOOKEEPER_DATALOG_DIR} \
    && chown -R zookeeper:zookeeper \
        ${ZOOKEEPER_HOME} \
        ${ZOOKEEPER_DATA_DIR} \
        ${ZOOKEEPER_DATALOG_DIR} \
    # cleanup
    # - remove unnecessary doc/src files 
    && rm -rf ${ZOOKEEPER_HOME}/src ${ZOOKEEPER_HOME}/docs \
    && rm -rf /root/.m2 \
    && apk del \
        .builddeps.1 \
    && echo
COPY entrypoint.sh /usr/local/bin/ 
COPY zoo.cfg       ${ZOOKEEPER_CONF_DIR}/
 
VOLUME ["${ZOOKEEPER_DATA_DIR}", "${ZOOKEEPER_DATALOG_DIR}"]

WORKDIR ${ZOOKEEPER_HOME}

EXPOSE 2181 2888 3888

ENTRYPOINT ["entrypoint.sh"]
CMD ["-server", "1", "1" ]
