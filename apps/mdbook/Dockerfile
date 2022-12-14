ARG VERSION

FROM rust:1.66.1-slim@sha256:1a2b7710dfd6213ca19ba1a4147c5c9cb68e257777929c6a138a19ec7a4c4b20 AS build

ENV CARGO_INSTALL_ROOT /usr/local/
ENV CARGO_TARGET_DIR /tmp/target/

ARG VERSION

RUN \
    apt-get -qq update \
    && \
    apt-get install -y \
        libssl-dev \
        pkg-config \
        ca-certificates \
        build-essential \
        make \
        perl \
        gcc \
        libc6-dev

RUN \
    cargo install --verbose \
        "mdbook@${VERSION}" \
        "mdbook-linkcheck" \
        "mdbook-admonish" \
        "mdbook-mermaid" \
        "mdbook-toc" \
        "mdbook-open-on-gh" \
        "mdbook-emojicodes" \
        "mdbook-regex"

FROM ghcr.io/onedr0p/ubuntu:rolling@sha256:6354de1c9666bf401b6fc18e49eea81a6d68655caf35727526ff0d76a909f98c

ARG TARGETPLATFORM
ARG TARGETARCH
ARG TARGETOS

LABEL dev.bjw-s.image.target_platform=$TARGETPLATFORM
LABEL dev.bjw-s.image.target_architecture=$TARGETARCH
LABEL dev.bjw-s.image.target_os=$TARGETOS
LABEL org.opencontainers.image.source="https://github.com/rust-lang/mdBook"

ENV \
    RUST_LOG=info

COPY --from=build /usr/local/bin/mdbook* /bin/

# hadolint ignore=DL3008,DL3015,SC2086
RUN \
    apt-get -qq update \
    && \
    apt-get -qq install --no-install-recommends -y \
        ca-certificates \
    && \
    rm -rf \
        /tmp/* \
        /var/lib/apt/lists/* \
        /var/tmp/

# Used when serving
EXPOSE 3000

WORKDIR /data
VOLUME [ "/data" ]
