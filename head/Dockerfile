# -*- mode: dockerfile; coding: utf-8 -*-
FROM debian:buster-slim AS build
RUN apt-get update && apt-get -y --no-install-recommends install \
      build-essential ca-certificates git rsync \
 && rm -rf /var/lib/apt/lists/*
WORKDIR /build
RUN git clone https://cm-gitlab.stanford.edu/bil/s7.git --depth 1
WORKDIR /build/s7
RUN echo '#define S7_LOAD_PATH "/usr/local/lib/s7"' >mus-config.h
RUN gcc -o s7 s7.c -ldl -lm -rdynamic -O2 -g -I . -D WITH_MAIN
RUN ./s7 libc.scm
RUN find . -name '*.scm' | rsync -va --files-from - ./ /usr/local/lib/s7/ \
 && cp -v libc_s7.so /usr/local/lib/s7/ \
 && cp -v s7 /usr/local/bin/

FROM debian:buster-slim
COPY --from=build /usr/local/ /usr/local/
RUN ln -s s7 /usr/local/bin/scheme-banner
CMD ["scheme-banner"]
