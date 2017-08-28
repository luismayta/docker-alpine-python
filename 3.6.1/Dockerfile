FROM alpine:3.6
MAINTAINER Luis Mayta <@slovacus>

ARG PREFIX_DIR=/usr/local/python3

# Replace the default install directory
# e.g. in vim :%s/\/usr\/local/$PREFIX_DIR/g
# Then tell the install to use that directory
# :%s/\.\/configure /\.\/configure --prefix=$PREFIX_DIR --enable-shared /g

RUN mkdir $PREFIX_DIR

ENV ALPINE_VERSION 3.6

ENV GPG_KEY 0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D

ENV PYTHON_VERSION 3.6.1

# if this is called "PIP_VERSION", pip explodes with "ValueError: invalid truth value '<VERSION>'"
ENV PYTHON_PIP_VERSION 9.0.1
env PYTHON_PIP_URL "https://bootstrap.pypa.io/get-pip.py"

# ensure local python is preferred over distribution python
ENV PATH $PREFIX_DIR/bin:$PATH

ENV LD_LIBRARY_PATH $PREFIX_DIR/lib

# http://bugs.python.org/issue19846
# > At the moment, setting "LANG=C" on a Linux system *fundamentally breaks Python 3*, and that's not OK.
ENV LANG C.UTF-8

# Install needed packages. Notes:
#   * Bash: Terminal sh.
ENV PACKAGES="\
  bash \
  ca-certificates \
"

# Install needed packages. Notes:
#   * Bash: Terminal sh.
ENV PACKAGES_DEV="\
    bzip2-dev \
    coreutils \
    dpkg-dev dpkg \
    expat-dev \
    gcc \
    gdbm-dev \
    libc-dev \
    linux-headers \
    make \
    ncurses-dev \
    openssl \
    openssl-dev \
    pax-utils \
    readline-dev \
    sqlite-dev \
    tcl-dev \
    tk \
    tk-dev \
    xz-dev \
    zlib-dev \
"

# Install needed packages. Notes:
#   * Bash: Terminal sh.
ENV PACKAGES_PYTHON="\
  gnupg \
  openssl \
  tar \
  xz \
"


RUN apk add --update \
    # install ca-certificates so that HTTPS works consistently
    # the other runtime dependencies for Python are installed later
    && apk add --no-cache $PACKAGES \
    && apk add --no-cache --virtual .fetch-deps $PACKAGES_PYTHON \

    # Download python
    && wget -O python.tar.xz https://www.python.org/ftp/python/$PYTHON_VERSION/Python-$PYTHON_VERSION.tar.xz \
    && wget -O python.tar.xz.asc https://www.python.org/ftp/python/$PYTHON_VERSION/Python-$PYTHON_VERSION.tar.xz.asc \
    # && export GNUPGHOME="$(mktemp -d)" \
    # && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys $GPG_KEY \
    # && gpg --batch --verify python.tar.xz.asc python.tar.xz \
    # && rm -rf $GNUPGHOME python.tar.xz.asc \
    && mkdir -p /usr/src/python \
    && tar -xJC /usr/src/python --strip-components=1 -f python.tar.xz \

    # Clean Files unnecesary
    && rm python.tar.xz \
    # Install packages developer
    && apk add --no-cache --virtual .fetch-deps $PACKAGES_DEV \
    # Install configure python
    && cd /usr/src/python \
    && gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)" \
    && env LDFLAGS="-L${PREFIX_DIR}/lib" ./configure --prefix=$PREFIX_DIR \
      --build="$gnuArch" \
      --enable-loadable-sqlite-extensions \
      --enable-shared \
      --with-system-expat \
      --with-system-ffi \
      --without-ensurepip \
    && make -j "$(nproc)" \
    && make install \

    && runDeps="$( \
      scanelf --needed --nobanner --recursive $PREFIX_DIR \
        | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
        | sort -u \
        | xargs -r apk info --installed \
        | sort -u \
    )" \
    && apk add --virtual .python-rundeps $runDeps \
    && find $PREFIX_DIR -depth \
        \( \
          \( -type d -a -name test -o -name tests \) \
          -o \
          \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
        \) -exec rm -rf '{}' + \

    # make some useful symlinks that are expected to exist
    && cd $PREFIX_DIR/bin \
    && ln -s idle3 idle \
    && ln -s pydoc3 pydoc \
    && ln -s python3 python \
    && ln -s python3-config python-config \
    # Install configure pip
    && wget "https://bootstrap.pypa.io/get-pip.py" \
    && python get-pip.py \
      --disable-pip-version-check \
      --no-cache-dir \
      "pip==$PYTHON_PIP_VERSION" \
    && pip --version \
    && find $PREFIX_DIR -depth \
      \( \
        \( -type d -a -name test -o -name tests \) \
        -o \
        \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
      \) -exec rm -rf '{}' +; \
    rm -f get-pip-.py \

    # Clean files unnecesary
    && apk del .fetch-deps \
    && rm -rf /usr/src/python \
    && rm -rf /var/cache/apk/*

# Define default command.
CMD ["bash"]