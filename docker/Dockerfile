FROM alpine:3.20

ARG FOLDSEEK_TAG=10-941cd33
ARG TARGETARCH

ENV FOLDSEEK_HOME=/opt/foldseek
ENV PATH=/usr/local/bin:/opt/foldseek/bin:${PATH}

RUN set -eux; \
    for attempt in 1 2 3; do \
      if apk add --no-cache \
            bash \
            ca-certificates \
            curl \
            gawk; then \
        break; \
      fi; \
      if [ "$attempt" = "3" ]; then exit 1; fi; \
      sleep 5; \
    done

WORKDIR /root

RUN set -eux; \
    arch="${TARGETARCH:-$(uname -m)}"; \
    download_dir="/tmp/foldseek-download"; \
    mkdir -p "${download_dir}" "${FOLDSEEK_HOME}/bin"; \
    cd "${download_dir}"; \
    base_url="https://github.com/steineggerlab/foldseek/releases/download/${FOLDSEEK_TAG}"; \
    case "${arch}" in \
      amd64|x86_64) \
        curl --retry 5 --retry-delay 2 --retry-all-errors -fL "${base_url}/foldseek-linux-avx2.tar.gz" -o foldseek-linux-avx2.tar.gz; \
        echo "e6a3e3860e76b0b7b858736f627fe680d4d7f0fc7298301d8a92b9f711d74ac7  foldseek-linux-avx2.tar.gz" | sha256sum -c -; \
        tar -xzf foldseek-linux-avx2.tar.gz; \
        cp -a foldseek/. "${FOLDSEEK_HOME}/"; \
        cp foldseek/bin/foldseek "${FOLDSEEK_HOME}/bin/foldseek_avx2"; \
        chmod 0755 "${FOLDSEEK_HOME}/bin/foldseek_avx2"; \
        rm -rf foldseek; \
        ;; \
      arm64|aarch64) \
        curl --retry 5 --retry-delay 2 --retry-all-errors -fL "${base_url}/foldseek-linux-arm64.tar.gz" -o foldseek-linux-arm64.tar.gz; \
        echo "c71907a7ea06cb6e93d8eb000b0c3689e5e25f5edeb3969e2553cdb8f42982c1  foldseek-linux-arm64.tar.gz" | sha256sum -c -; \
        tar -xzf foldseek-linux-arm64.tar.gz; \
        cp -a foldseek/. "${FOLDSEEK_HOME}/"; \
        cp foldseek/bin/foldseek "${FOLDSEEK_HOME}/bin/foldseek_arm64"; \
        chmod 0755 "${FOLDSEEK_HOME}/bin/foldseek_arm64"; \
        rm -rf foldseek; \
        ;; \
      *) \
        echo "Unsupported TARGETARCH: ${arch}" >&2; \
        exit 1; \
        ;; \
    esac; \
    printf '%s\n' \
      '#!/bin/sh' \
      'arch="$(uname -m)"' \
      '' \
      'case "${arch}" in' \
      '  aarch64|arm64)' \
      '    binary="/opt/foldseek/bin/foldseek_arm64"' \
      '    ;;' \
      '  x86_64|amd64)' \
      '    flags=""' \
      '    if [ -r /proc/cpuinfo ]; then' \
      '      flags="$(grep -m 1 "^flags" /proc/cpuinfo || true)"' \
      '    fi' \
      '    case "${flags}" in' \
      '      *avx2*) binary="/opt/foldseek/bin/foldseek_avx2" ;;' \
      '      *) echo "Foldseek official linux/amd64 CPU binary requires AVX2. Use linux/arm64 on ARM hosts or a custom non-AVX2 build." >&2; exit 1 ;;' \
      '    esac' \
      '    ;;' \
      '  *)' \
      '    echo "Unsupported runtime architecture: ${arch}" >&2' \
      '    exit 1' \
      '    ;;' \
      'esac' \
      '' \
      'exec "${binary}" "$@"' \
      > /usr/local/bin/foldseek; \
    chmod 0755 /usr/local/bin/foldseek; \
    ln -sf /usr/local/bin/foldseek "${FOLDSEEK_HOME}/bin/foldseek"; \
    rm -rf "${download_dir}"

RUN foldseek version | grep -Fx "941cd33ff0771cd2e3f144e3293e22a2b87e9fda" >/dev/null \
 && foldseek 2>&1 | grep -F "foldseek Version: 941cd33ff0771cd2e3f144e3293e22a2b87e9fda" >/dev/null \
 && foldseek easy-search 2>&1 | grep -F "usage: foldseek easy-search" >/dev/null

ENV TAFFISH_ENV=TAFFISH
ENV TAFFISH_NAME=foldseek
