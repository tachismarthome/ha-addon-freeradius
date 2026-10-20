ARG BUILD_FROM=ghcr.io/home-assistant/base:latest
FROM ${BUILD_FROM}

# freeradius       - RADIUS server
# freeradius-eap   - EAP modules (PEAP / MSCHAPv2) required for WPA-Enterprise
# freeradius-utils - radtest and friends for troubleshooting
# jq               - parse /data/options.json
# openssl          - generate the EAP certificates at first start
RUN apk add --no-cache \
    bash \
    jq \
    openssl \
    freeradius \
    freeradius-eap \
    freeradius-utils

COPY run.sh /run.sh
RUN chmod a+x /run.sh

CMD [ "/run.sh" ]
