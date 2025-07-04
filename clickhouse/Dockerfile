FROM ubuntu:20.04

ARG CLICKHOUSE_VERSION=22.2.2.1
ARG CLICKHOUSE_GOSU_VERSION=1.14

ARG REPO_CHANNEL="stable"
ARG REPOSITORY="deb https://packages.clickhouse.com/deb ${REPO_CHANNEL} main"

RUN apt-get update \
    && apt-get install --yes --no-install-recommends \
        apt-transport-https \
        ca-certificates \
        dirmngr \
        gnupg \
        gnupg2 \
        locales \
        tzdata \
        wget \
    && mkdir -p /etc/apt/sources.list.d \
    && apt-key adv --keyserver keyserver.ubuntu.com --recv 8919F6BD2B48D754 \
    && echo ${REPOSITORY} > /etc/apt/sources.list.d/clickhouse.list \
    && apt-get update \
    && apt-get --yes -o "Dpkg::Options::=--force-confdef" -o "Dpkg::Options::=--force-confold" upgrade \
    && env DEBIAN_FRONTEND=noninteractive \
        apt-get install --allow-unauthenticated --yes --no-install-recommends \
            clickhouse-common-static=$CLICKHOUSE_VERSION \
            clickhouse-client=$CLICKHOUSE_VERSION \
            clickhouse-server=$CLICKHOUSE_VERSION \
    && rm -rf \
        /var/lib/apt/lists/* \
        /var/cache/debconf \
        /tmp/* \
    && apt-get clean

ADD https://github.com/tianon/gosu/releases/download/$CLICKHOUSE_GOSU_VERSION/gosu-amd64 /bin/gosu

RUN locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

RUN mkdir /docker-entrypoint-initdb.d

COPY docker_related_config.xml /etc/clickhouse-server/config.d/
COPY config.xml /etc/clickhouse-server/config.xml
COPY entrypoint.sh /entrypoint.sh

RUN chmod +x \
    /entrypoint.sh \
    /bin/gosu

EXPOSE 9000 8123 9009
VOLUME /var/lib/clickhouse

ENV CLICKHOUSE_CONFIG /etc/clickhouse-server/config.xml
ENV CLICKHOUSE_USER ${CLICKHOUSE_USER}
ENV CLICKHOUSE_PASSWORD ${CLICKHOUSE_PASSWORD}

ENTRYPOINT ["/entrypoint.sh"]
