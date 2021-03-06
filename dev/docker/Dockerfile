FROM alpine:3.6

# Based on: https://github.com/jfloff/alpine-python/blob/master/2.7/slim/Dockerfile

ENV PACKAGES="\
  musl \
  linux-headers \
  build-base \
  bash \
  ca-certificates \
  python \
  python-dev \
  py-setuptools \
  python-dev \
  libxml2-dev \
  py-lxml \
  libffi-dev \
  libssl1.0 \
  openssl-dev \
"

COPY requirements.txt /tmp/requirements.txt

RUN echo \
  # Add the packages, with a CDN-breakage fallback if needed
  && echo https://nl.alpinelinux.org/alpine/v3.6/main/ > /etc/apk/repositories \
  && echo https://nl.alpinelinux.org/alpine/v3.6/community/ >> /etc/apk/repositories \
  && apk add --no-cache $PACKAGES || \
    (sed -i -e 's/dl-cdn/dl-4/g' /etc/apk/repositories && apk add --no-cache $PACKAGES) \

  # make some useful symlinks that are expected to exist
  && if [[ ! -e /usr/bin/python ]];        then ln -sf /usr/bin/python2.7 /usr/bin/python; fi \
  && if [[ ! -e /usr/bin/python-config ]]; then ln -sf /usr/bin/python2.7-config /usr/bin/python-config; fi \
  && if [[ ! -e /usr/bin/easy_install ]];  then ln -sf /usr/bin/easy_install-2.7 /usr/bin/easy_install; fi \

  # Install and upgrade Pip
  && easy_install pip \
  && pip install --upgrade pip \
  && if [[ ! -e /usr/bin/pip ]]; then ln -sf /usr/bin/pip2.7 /usr/bin/pip; fi \

  # Install python dependencies
  && pip install -r /tmp/requirements.txt

# Install the crawler
WORKDIR /app
ADD . ./dev/docker/run_with_interval.sh /app/

ENTRYPOINT ["python", "crawler.py"]
CMD ["--region", "amsterdam"]
