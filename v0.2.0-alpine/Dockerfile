FROM erlang:21-alpine

LABEL maintainer="ilyas@toumlilt.com"

ENV HANDOFF_PORT "8099"
ENV PB_PORT "8087"
ENV PB_IP "0.0.0.0"
ENV PBSUB_PORT "8086"
ENV LOGREADER_PORT "8085"
ENV RING_STATE_DIR "data/ring"
ENV PLATFORM_DATA_DIR "data"
ENV NODE_NAME "antidote@127.0.0.1"
ENV SHORT_NAME "false"
ENV ANTIDOTE_REPO "https://github.com/ilyasToumlilt/antidote.git"
# TARGET can be a branch name or release tag
ENV ANTIDOTE_REPO_TARGET "v0.2.0"

RUN set -xe \
  && apk --no-cache --update upgrade busybox musl \
  && apk --no-cache add bash coreutils git curl build-base openssl ca-certificates perl \
  && cd /tmp \
  && git clone -b "$ANTIDOTE_REPO_TARGET" --single-branch --depth 1 $ANTIDOTE_REPO \
  && cd antidote \
  && make rel \
  && mkdir -p /opt \
  && cp -R _build/default/rel/antidote /opt/ \
  && sed -e '$i,{kernel, [{inet_dist_listen_min, 9100}, {inet_dist_listen_max, 9100}]}' /tmp/antidote/_build/default/rel/antidote/releases/0.0.2/sys.config > /opt/antidote/releases/0.0.2/sys.config \
  && apk del git curl perl \
  && rm -rf /tmp/antidote /var/lib/apt/lists/*

ADD ./start_and_attach.sh /opt/antidote/
ADD ./entrypoint.sh /

RUN chmod a+x /opt/antidote/start_and_attach.sh \
  && chmod a+x /entrypoint.sh

# Distributed Erlang Port Mapper
EXPOSE 4369
# Ports for Antidote
EXPOSE 8085 8086 8087 8099

# Antidote RPC
EXPOSE 9100

VOLUME /opt/antidote/data

ENTRYPOINT ["/entrypoint.sh"]

CMD ["/opt/antidote/start_and_attach.sh"]
