ARG BUILD_FROM=hassioaddons/ubuntu-base:2.2.1
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Copy root filesystem
COPY rootfs /

RUN \
    # Temporarily move nginx.conf (otherwise dpkg fails)
    mv /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bkp \
    # Install add-on dependencies
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        # Python for esphomeyaml
        python \
        python-pip \
        python-setuptools \
        # Python Pillow for display component
        python-pil \
        # Git for esphomelib downloads
        git \
        # Ping for dashboard online/offline status
        iputils-ping \
        # NGINX proxy
        nginx \
    \
    && mv /etc/nginx/nginx.conf.bkp /etc/nginx/nginx.conf \
    \
    && pip2 install --no-cache-dir --no-binary :all: https://github.com/OttoWinter/esphomeyaml/archive/dev.zip \
    \
    # Change some platformio settings
    && platformio settings set enable_telemetry No \
    && platformio settings set check_libraries_interval 1000000 \
    && platformio settings set check_platformio_interval 1000000 \
    && platformio settings set check_platforms_interval 1000000 \
    \
    # Build an empty platformio project to force platformio to install all fw build dependencies
    # The return-code will be non-zero since there's nothing to build.
    && (platformio run -d /opt/pio; echo "Done") \
    \
    # Cleanup
    && rm -fr \
        /tmp/* \
        /var/{cache,log}/* \
        /var/lib/apt/lists/* \
        /opt/pio/

# Build arugments
ARG BUILD_ARCH=amd64
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="esphomeyaml-edge" \
    io.hass.description="Manage and program ESP8266/ESP32 microcontrollers through YAML configuration files" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Otto Winter <contact@otto-winter.com>" \
    org.label-schema.description="Manage and program ESP8266/ESP32 microcontrollers through YAML configuration files" \
    org.label-schema.build-date=${BUILD_DATE} \
    org.label-schema.name="esphomeyaml-edge" \
    org.label-schema.schema-version="1.0" \
    org.label-schema.url="https://esphomelib.com" \
    org.label-schema.usage="https://github.com/OttoWinter/esphomeyaml/tree/dev/esphomeyaml-edge/README.md" \
    org.label-schema.vcs-ref=${BUILD_REF} \
    org.label-schema.vcs-url="https://github.com/OttoWinter/esphomeyaml" \
    org.label-schema.vendor="esphomelib"
