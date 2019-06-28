+++
date = "2019-06-28"
title = "Running AWSCLI (ALPINE)"
slug = "Running AWSCLI (ALPINE)"
tags = [
    "AWS",
    "Cloud"
]
categories = []
series = []
+++

##### Running the docker container or using the steps to get awscli quickly 
###### Docker Image

```FROM alpine:3.7

ENV AWSCLI_VERSION "1.14.10"

RUN apk add --update \
    python \
    python-dev \
    py-pip \
    build-base \
    && pip install awscli==$AWSCLI_VERSION --upgrade --user \
    && apk --purge -v del py-pip \
    && rm -rf /var/cache/apk/*

ENTRYPOINT ["/root/.local/bin/aws"]
```
