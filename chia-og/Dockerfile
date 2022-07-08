FROM ubuntu:20.04

RUN groupadd -r chia && useradd -r -m -g chia chia && usermod -a -G users,chia chia \
  && export ARCH=$(dpkg --print-architecture) \
  && if [ "$ARCH" = "amd64" ] ; then export ARCH=amd64; fi \
  && if [ "$ARCH" = "arm64" ] ; then export ARCH=aarch64; fi \
  && if [ "$ARCH" = "arm" ] ; then export ARCH=arm; fi

ARG FILE=tmp/linux/*-
ENV PATH=$PATH:/opt
ENV TZ=Asia/Shanghai
WORKDIR /opt

COPY $FILE$ARCH /opt

RUN apt-get -qq update \
    && apt-get -qq install -y --no-install-recommends ca-certificates curl gosu tini \
    && cp /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo $TZ > /etc/timezone \
    && cd /opt/ \
    && mv hpool-* hpool-chia-miner \
    && ls -al /opt/ \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /var/cache/apt/*

COPY docker-entrypoint.sh /opt/entrypoint.sh

ENTRYPOINT ["tini", "--", "entrypoint.sh"]

CMD ["hpool-chia-miner"]
