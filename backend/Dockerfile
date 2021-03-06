FROM --platform=linux/arm64 python:3.10

ARG OMXPLAYER_DEB_URL="http://archive.raspberrypi.org/debian/pool/main/o/omxplayer/omxplayer_20190723+gitf543a0d-1+bullseye_armhf.deb" \
    FIRMWARE_VC_LIBS_VERSION="ae17aabd" \
    FIRMWARE_VC_LIBS="libbcm_host.so libbrcmEGL.so libbrcmGLESv2.so libopenmaxil.so libvchiq_arm.so libvcos.so" \
    POETRY_VERSION=1.1.14

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    POETRY_VIRTUALENVS_CREATE=false

RUN mkdir -p /tmp/firmware /opt/vc/lib \
    && wget -qO - "https://github.com/raspberrypi/firmware/tarball/${FIRMWARE_VC_LIBS_VERSION}" \
        | tar xz --strip=1 -C /tmp/firmware \
    && for FIRMWARE_VC_LIB in $FIRMWARE_VC_LIBS ; do \
        mv -v "/tmp/firmware/opt/vc/lib/$FIRMWARE_VC_LIB" /opt/vc/lib ; \
    done \
    && rm -rf /tmp/firmware

RUN curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python -
ENV PATH="/root/.poetry/bin:${PATH}"

# Need to add armhf because omxplayer is armhf, not arm64
RUN dpkg --add-architecture armhf \
    # Add raspberry pi debian apt repo
    && wget -qO - https://archive.raspberrypi.org/debian/raspberrypi.gpg.key \
        | gpg --dearmor > /etc/apt/trusted.gpg.d/raspberrypi.gpg \
    && echo "deb http://archive.raspberrypi.org/debian/ $(sh -c '. /etc/os-release; echo $VERSION_CODENAME') main" \
        > /etc/apt/sources.list.d/raspi.list \
    && apt-get update \
    && apt-get upgrade -y \
    && wget -qO /tmp/omxplayer.deb "$OMXPLAYER_DEB_URL" \
    && apt-get install --no-install-recommends -y \
        # To mute/unmute
        alsa-utils \
        # To set background
        fbi \
        ffmpeg \
        # For "killall"
        psmisc \
        /tmp/omxplayer.deb \
    && rm -rf /var/lib/apt/lists/* /tmp/omxplayer.deb

COPY pyproject.toml poetry.lock /app/
WORKDIR /app
RUN poetry install

COPY . /app
CMD ["./run.sh"]
