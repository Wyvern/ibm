FROM s390x/alpine
LABEL maintainer="ifree.net"
# prepare go env
ENV GOPATH /go
ENV NAME cloud-torrent
ENV PACKAGE github.com/jpillora/$NAME
ENV PACKAGE_DIR $GOPATH/src/$PACKAGE
ENV GOLANG_VERSION 1.11.4
ENV GOLANG_SRC_URL https://dl.google.com/go/go$GOLANG_VERSION.src.tar.gz
ENV PATH $GOPATH/bin:/usr/local/go/bin:$PATH
ENV CGO_ENABLED 0
# in one step (to prevent creating superfluous layers):
# 1. fetch and install temporary build programs,
# 2. build cloud-torrent alpine binary
# 3. remove build programs
RUN set -ex \
	&& apk update \
	&& apk add ca-certificates \
	&& apk add --no-cache --virtual .build-deps \
	bash \
	gcc \
	musl-dev \
	openssl \
	git \
	go \
	curl \
	&& export GOROOT_BOOTSTRAP="$(go env GOROOT)" \
	&& wget -q "$GOLANG_SRC_URL" -O golang.tar.gz \
	&& tar -C /usr/local -xzf golang.tar.gz \
	&& rm golang.tar.gz \
	&& cd /usr/local/go/src \
	&& ./make.bash \
	&& mkdir -p $PACKAGE_DIR \
	&& git clone https://$PACKAGE.git $PACKAGE_DIR \
	&& cd $PACKAGE_DIR \
	&& go build -ldflags "-X main.VERSION=$(git describe --abbrev=0 --tags)" -o /usr/local/bin/$NAME \
	&& apk del .build-deps \
	&& rm -rf $GOPATH /usr/local/go
#run!
ENTRYPOINT ["cloud-torrent"]
