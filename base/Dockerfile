FROM ubuntu:xenial-20190222

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update -y && \
    apt-get dist-upgrade -y && \
    apt-get install -y \
    autoconf \
    build-essential \
    imagemagick \
    libbz2-dev \
    libcurl4-openssl-dev \
    libevent-dev \
    libffi-dev \
    libglib2.0-dev \
    libjpeg-dev \
    libmagickcore-dev \
    libmagickwand-dev \
    libmysqlclient-dev \
    libncurses-dev \
    libpq-dev \
    libreadline-dev \
    libsqlite3-dev \
    libssl-dev \
    libxml2-dev \
    libxslt-dev \
    libyaml-dev \
    wget \
    zlib1g-dev \
    python-software-properties \
    software-properties-common \
    patchutils \
    curl \
    procps \
    language-pack-en \
    git \
    unzip \
    sudo \
  && rm -rf /var/lib/apt/lists/*

# Add locales
RUN locale-gen en_US.UTF-8

# Set default locale
ENV LC_ALL=en_US.UTF-8 \
    LANG=en_US.UTF-8

RUN update-locale en_US.UTF-8

# Install Ruby to use ruby script in node. e.g.) to expand glob
# The checksum is defined in https://cache.ruby-lang.org/pub/ruby/index.txt
ENV RUBY_MAJOR=2.5 \
    RUBY_VERSION=2.5.1 \
    RUBY_DOWNLOAD_SHA256=0f5d20f012baca865381a055e73f22db814615fee3c68083182cb78a4b3b30cb

# some of ruby's build scripts are written in ruby
# we purge this later to make sure our final image uses what we just built
RUN apt-get update -y \
  && apt-get install -y bison ruby \
  && rm -rf /var/lib/apt/lists/* \
  && mkdir -p /usr/src/ruby \
  && curl -SL -o /tmp/ruby.tar.bz2 "https://cache.ruby-lang.org/pub/ruby/$RUBY_MAJOR/ruby-$RUBY_VERSION.tar.bz2" \
  && echo "$RUBY_DOWNLOAD_SHA256 */tmp/ruby.tar.bz2" | sha256sum --check --strict \
  && tar -xjf /tmp/ruby.tar.bz2 -C /usr/src/ruby --strip-components=1 \
  && rm -f /tmp/ruby.tar.bz2 \
  && cd /usr/src/ruby \
  && autoconf \
  && ./configure --disable-install-doc \
  && make -j"$(nproc)" \
  && apt-get purge -y --auto-remove bison ruby \
  && make install \
  && rm -r /usr/src/ruby

ADD gemrc /usr/local/etc/
ENV GEM_HOME /usr/local/bundle
ENV PATH $GEM_HOME/bin:$PATH

# Bundler 2 requires rubygems 3.x
# `gem update --system` will be removed after upgrade Ruby version to 2.6 since
# the Ruby version uses rubygems v3 to default.
# See: https://github.com/ruby/ruby/commit/92e726628e3baf4acb1a349089aa4d3278c2ad40
RUN gem update --system

# Create a user and set relevant environment variables
ENV RUNNER_USER analyzer_runner
ENV RUNNER_USER_HOME /home/$RUNNER_USER
RUN adduser --system --home $RUNNER_USER_HOME $RUNNER_USER \
    && echo 'Defaults always_set_home' >> /etc/sudoers \
    && echo 'Defaults env_keep += "GEM_HOME"' >> /etc/sudoers \
    && sed -i -e '/secure_path/d' /etc/sudoers \
    && echo "Defaults secure_path=\"$GEM_HOME/bin:/usr/local/bin:/usr/bin:/bin\"" >> /etc/sudoers

# Install bundler v1.17.3 not but bundler v2 since runner applications probably parse Gemfile.lock of v1.
# And, we will keep bundler v1 until the version is out of use.
# See: https://bundler.io/blog/2019/01/03/announcing-bundler-2.html
RUN gem install bundler --version 1.17.3 \
  && gem install bundler --version 2.0.2

WORKDIR /analyzer
