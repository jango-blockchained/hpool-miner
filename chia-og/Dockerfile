FROM alpine:3.15 as download

ARG FILE=tmp/linux/*-
ENV TZ=Asia/Shanghai

RUN apk update && apk --no-cache add bash tzdata dpkg \
  && cp /usr/share/zoneinfo/$TZ /etc/localtime \
  && echo $TZ > /etc/timezone \
  && export ARCH=$(dpkg --print-architecture) \
  && if [ "$ARCH" = "amd64" ] ; then export ARCH=amd64; fi \
  && if [ "$ARCH" = "arm64" ] ; then export ARCH=aarch64; fi \
  && if [ "$ARCH" = "arm" ] ; then export ARCH=arm; fi

COPY $FILE$ARCH /opt

FROM ubuntu:20.04

ENV PATH=$PATH:/opt
WORKDIR /opt

COPY --from=download /etc/localtime /etc
COPY --from=download /etc/timezone /etc
COPY --from=download /opt/hpool-* /opt/hpool-chia-miner

RUN apt-get -qq update \
    && apt-get -qq install -y --no-install-recommends ca-certificates curl gosu tini \
    && groupadd -r chia \
    && useradd -r -m -g chia chia \
    && usermod -a -G users,chia chia \
    && ls -al /opt/ \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/cache/apt/*

COPY docker-entrypoint.sh /opt/entrypoint.sh

ENTRYPOINT ["tini", "--", "entrypoint.sh"]

CMD ["hpool-chia-miner"]
