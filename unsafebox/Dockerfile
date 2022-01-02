# Copyright 2017 The Go Authors. All rights reserved.
# Use of this source code is governed by a BSD-style
# license that can be found in the LICENSE file.
FROM debian:bullseye as builder

ENV GOPATH /go
ENV GOCACHE /go/.cache
ENV PATH /usr/local/go/bin:$GOPATH/bin:$PATH
ENV GOROOT_BOOTSTRAP /usr/local/gobootstrap
ENV GO_VERSION 1.17.5

RUN apt-get update && \
  apt-get install -y ca-certificates curl

# Get the Go binary.
RUN curl -sSL https://dl.google.com/go/go$GO_VERSION.linux-amd64.tar.gz -o /tmp/go.tar.gz
RUN curl -sSL https://dl.google.com/go/go$GO_VERSION.linux-amd64.tar.gz.sha256 -o /tmp/go.tar.gz.sha256
RUN echo "$(cat /tmp/go.tar.gz.sha256) /tmp/go.tar.gz" | sha256sum -c -
RUN tar -C /usr/local/ -vxzf /tmp/go.tar.gz

# Add and compile playground daemon
WORKDIR /go/src/playground/
COPY . .

RUN go build -o /go/bin/playground .

FROM debian:bullseye

RUN apt-get update && \
  apt-get install -y ca-certificates git --no-install-recommends

COPY --from=builder /usr/local/go /usr/local/go

ENV GOPATH /go
ENV GOCACHE /tmp/.cache

ENV PATH /usr/local/go/bin:$GOPATH/bin:$PATH

RUN mkdir /app

COPY --from=builder /go/bin/playground /app/
WORKDIR /app

# Run tests
RUN /app/playground test

EXPOSE 8080
ENTRYPOINT ["/app/playground"]
