# !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
# NOTE: DO *NOT* EDIT THIS FILE.  IT IS GENERATED.
# PLEASE UPDATE Dockerfile.erb INSTEAD OF THIS FILE
# !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
FROM php:7.3.9-buster

ENV GLOBAL_RUBY_VERSION 2.6.4
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

COPY base/bin/prepare /tmp/work/
RUN /tmp/work/prepare && rm -rf /tmp/work


# PHP configuration
RUN mv $PHP_INI_DIR/php.ini-production $PHP_INI_DIR/php.ini
COPY php/sider.ini $PHP_INI_DIR/conf.d/sider.ini

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

ENV COMPOSER_HOME $RUNNER_USER_HOME/.composer
ENV COMPOSER_VENDOR_BIN $COMPOSER_HOME/vendor/bin
ENV PATH $COMPOSER_VENDOR_BIN:$PATH

# Following instructions should be run by $RUNNER_USER
USER $RUNNER_USER
RUN mkdir -p $COMPOSER_VENDOR_BIN

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
    rbenv versions && \
    test $(ruby -e 'puts RUBY_VERSION') = "$GLOBAL_RUBY_VERSION" && \
    echo 'install: --no-document' >> ~/.gemrc && \
    echo 'update: --no-document' >> ~/.gemrc && \
    # NOTE: Install bundler v1 as well as v2 because runner applications need them
    #       in order to parse both versions of Gemfile.lock.
    #       See https://bundler.io/blog/2019/01/03/announcing-bundler-2.html
    gem install bundler --version $BUNDLER1_VERSION && \
    gem install bundler --version $BUNDLER2_VERSION && \
    bundle -v | grep -q "$BUNDLER2_VERSION"

ENV PATH $RUNNER_USER_HOME/.rbenv/shims:$RUNNER_USER_HOME/.rbenv/bin:$PATH

# Test if rbenv is installed successfully
RUN rbenv -v


CMD php -v && composer --version
