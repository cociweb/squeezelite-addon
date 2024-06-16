ARG BUILD_FROM=ghcr.io/home-assistant/amd64-base-debian:bookworm
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Set correct environment variables
ENV DEBIAN_FRONTEND="noninteractive"
ENV LC_ALL="C.UTF-8" 
ENV LANG="en_US.UTF-8" 
ENV LANGUAGE="en_US.UTF-8" 

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_VERSION

# Upgrading the operating system inside the container
RUN apt-get update && apt-get upgrade -y -o Dpkg::Options::="--force-confold"

# Install Squeezelite
RUN apt-get -qq -y update && \
	if [ "${BUILD_ARCH}" = "i386" ]; then PACKAGES="alsa-base alsa-utils libasound2-data libasound2-dev libasound2-plugins libasound2"; fi && \
	if [ "${BUILD_ARCH}" = "aarch64" ]; then PACKAGES="libasound2-dev libasound2-plugins libasound2-data libflac-dev libmad0-dev libvorbis-dev libmpg123-dev libfaad-dev libsox-dev libsoxr-dev libavformat-dev liblirc-dev libssl-dev libopusfile-dev faad"; fi && \
	if [ "${BUILD_ARCH}" = "amd64" ]; then PACKAGES="alsa-base alsa-utils libasound2-data libasound2-dev libasound2-plugins libasound2"; fi && \
 	if [ "${BUILD_ARCH}" = "armhf" ]; then PACKAGES="libasound2-dev libasound2-plugins libasound2-data libflac-dev libmad0-dev libvorbis-dev libmpg123-dev libfaad-dev libsox-dev libsoxr-dev libavformat-dev liblirc-dev libssl-dev libopusfile-dev faad"; fi && \
#	if [ "${BUILD_ARCH}" = "armv7" ]; then PACKAGES="libasound2-dev libasound2-plugins libasound2-data libflac-dev libmad0-dev libvorbis-dev libmpg123-dev libfaad-dev libsox-dev libsoxr-dev libavformat-dev liblirc-dev libssl-dev libopusfile-dev faad"; fi && \
    apt-get -qq -y install ${PACKAGES}
	
# Clean up APT when done.
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Copy root filesystem
COPY rootfs /

#Create temporary directory
RUN rm -fr /tmp/squeezelitetemp && \
	mkdir -p /tmp/squeezelitetemp
	
#Copy Squeezelite into temporary directory
COPY /bin/${BUILD_ARCH}/* /tmp/squeezelitetemp/

#Ubuntu ARM64 2.29 libc from https://answers.launchpad.net/ubuntu/+source/glibc/2.29-0ubuntu2/+build/16599429

#Extract file and copy it
RUN	cd /tmp/squeezelitetemp && \
	tar -xvzf /tmp/squeezelitetemp/*.tar.gz && \
	mv /tmp/squeezelitetemp/squeezelite /usr/local/bin/squeezelite && \
	chmod a+x /usr/local/bin/squeezelite && \
	chown root:root /usr/local/bin/squeezelite && \
#	if [ "${BUILD_ARCH}" = "aarch64" ]; then dpkg -i libc6_2.29-0ubuntu2_arm64.deb; fi && \
	cd / && \
	rm -fr /tmp/squeezelitetemp

# Labels
LABEL \
    io.hass.name="Squeezelite" \
    io.hass.description="Standalone Squeezelite client" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="cociweb" \
    org.opencontainers.image.title="Squeezelite" \
    org.opencontainers.image.description="Standalone Squeezelite client" \
    org.opencontainers.image.vendor="cociweb" \
    org.opencontainers.image.authors="cociweb" \
    org.opencontainers.image.licenses="APACHE" \
    org.opencontainers.image.url="https://github.com/cociweb/" \
    org.opencontainers.image.source="https://github.com/cociweb/squeezelite-addon" \
    org.opencontainers.image.documentation="https://github.com/cociweb/squeezelite-addon/blob/main/squeezelite/DOCS.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
