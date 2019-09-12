# !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
# NOTE: DO *NOT* EDIT THIS FILE.  IT IS GENERATED.
# PLEASE UPDATE Dockerfile.erb INSTEAD OF THIS FILE
# !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
FROM debian:buster-20190910

ENV GLOBAL_RUBY_VERSION 2.5.5
ENV BUNDLER1_VERSION 1.17.3
ENV BUNDLER2_VERSION 2.0.2
ENV DEVON_REX_RBENV_VERSION v1.1.2
ENV DEVON_REX_RUBY_BUILD_VERSION v20190828
ENV LANG en_US.UTF-8
ENV LANGUAGE $LANG
ENV LC_ALL $LANG
ENV RUNNER_USER analyzer_runner
ENV RUNNER_USER_HOME /home/$RUNNER_USER
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update -y && \
    apt-get install -y --no-install-recommends \
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
        libmariadb-dev-compat \
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
        software-properties-common \
        patchutils \
        curl \
        procps \
        locales \
        tzdata \
        git \
        openssh-client \
        unzip \
        sudo \
        ;

# Configure locale
RUN sed -i -e "s/# ${LANGUAGE}/${LANGUAGE}/" /etc/locale.gen && \
    locale-gen $LANGUAGE && \
    dpkg-reconfigure --frontend noninteractive locales

# Clean up
RUN apt-get -qyy autoremove && \
    rm -rf /var/lib/apt/lists/* && \
    apt-get -qyy clean

# Create a user
RUN adduser --system --shell /bin/bash --home $RUNNER_USER_HOME $RUNNER_USER
RUN install -o $RUNNER_USER -d /analyzer
WORKDIR /analyzer


# Following instructions should be run by $RUNNER_USER
USER $RUNNER_USER

RUN test $(whoami) = "$RUNNER_USER" && \
    git clone https://github.com/rbenv/rbenv.git ~/.rbenv && \
    cd ~/.rbenv && \
    git checkout --quiet $DEVON_REX_RBENV_VERSION && \
    src/configure && \
    make -j $(nproc) -C src && \
    echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile && \
    echo 'eval "$(rbenv init -)"' >> ~/.bash_profile && \
    . ~/.bash_profile && \
    RBENV_ROOT=$(rbenv root) && \
    mkdir -p ${RBENV_ROOT}/plugins && \
    git clone https://github.com/rbenv/ruby-build.git ${RBENV_ROOT}/plugins/ruby-build && \
    cd ${RBENV_ROOT}/plugins/ruby-build && \
    git checkout --quiet $DEVON_REX_RUBY_BUILD_VERSION && \
    rbenv install $GLOBAL_RUBY_VERSION && \
    rbenv global $GLOBAL_RUBY_VERSION && \
    echo 'install: --no-document' >> ~/.gemrc && \
    echo 'update: --no-document' >> ~/.gemrc

ENV PATH $RUNNER_USER_HOME/.rbenv/versions/$GLOBAL_RUBY_VERSION/bin:$RUNNER_USER_HOME/.rbenv/bin:$PATH
RUN rbenv -v

# NOTE: Install bundler v1 as well as v2 because runner applications need them
#       in order to parse both versions of Gemfile.lock.
#       See https://bundler.io/blog/2019/01/03/announcing-bundler-2.html
RUN gem install bundler --version $BUNDLER1_VERSION && \
    gem install bundler --version $BUNDLER2_VERSION


# Install pyenv
USER $RUNNER_USER
RUN curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash
ARG PYENV_SHIMS_BIN="$RUNNER_USER_HOME/.pyenv/shims"
ARG PYENV_BIN="$RUNNER_USER_HOME/.pyenv/bin"
ENV PATH $PYENV_SHIMS_BIN:$PYENV_BIN:$PATH

# Install python 2 and 3
RUN pyenv install 2.7.16 \
  && pyenv install 3.7.4 \
  && pyenv rehash        \
  && pyenv global 3.7.4
CMD pyenv versions
