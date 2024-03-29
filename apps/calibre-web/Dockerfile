FROM ghcr.io/cdloh/calibre-bare:7.7.0@sha256:76e3003c442960c48ff5ff20fddfefd8c27736e884039cfa92edf24483a45d86 as calibre
FROM ghcr.io/bjw-s/kepubify:4.0.4@sha256:cd20b7a1079ca33a41eea5f35e2bc82200904888d52a19d0eb5a1164feda64e4 as kepubify

FROM ghcr.io/onedr0p/ubuntu:rolling@sha256:a79a3386914dfdf37d4d03173ab8bba51512305b3863ab178f2520ef27981083

ARG TARGETPLATFORM
ARG TARGETARCH
ARG TARGETOS

ARG VERSION

LABEL dev.bjw-s.image.target_platform=$TARGETPLATFORM
LABEL dev.bjw-s.image.target_architecture=$TARGETARCH
LABEL dev.bjw-s.image.target_os=$TARGETOS
LABEL org.opencontainers.image.source="https://github.com/janeczku/calibre-web"

ENV \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_ROOT_USER_ACTION=ignore \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

ENV \
    CALIBRE_DBPATH=/config

# hadolint ignore=DL3008,DL3015,SC2086
RUN \
    apt-get -qq update \
    && \
    apt-get -qq install -y \
        ghostscript \
        libldap-2.5-0 \
        libsasl2-2 \
        libxi6 \
        libxslt1.1 \
        python3 \
        python3-pip \
        python3-pkg-resources \
    && \
    # Dev
    apt-get -qq install -y \
        libldap2-dev \
        libsasl2-dev \
        python3-dev \
        gcc \
        g++ \
    && \
    # Download calibre-web release
    curl -fsSL https://github.com/janeczku/calibre-web/archive/${VERSION}.tar.gz \
        | tar xzf - -C /app --strip-components=1 \
    # Install required python packages
    && pip install --upgrade pip \
    && pip install --upgrade --requirement /app/requirements.txt \
    && pip install --upgrade --requirement /app/optional-requirements.txt \
    # Cleanup
    && apt-get purge -y --auto-remove libldap2-dev libsasl2-dev python3-dev gcc g++\
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
    && apt-get autoremove -y \
    && apt-get clean \
    && \
    rm -rf \
        /tmp/* \
        /var/lib/apt/lists/* \
        /var/tmp/ \
    # Set permissions
    && chown -R 568:568 /app \
    && chmod -R 755 /app \
    && printf "umask %d" "${UMASK}" >> /etc/bash.bashrc

COPY --from=calibre / /opt/calibre
COPY --from=kepubify /app/kepubify /opt/kepubify/kepubify

USER kah
COPY ./entrypoint.sh /entrypoint.sh
CMD ["/entrypoint.sh"]
