# !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
# NOTE: DO *NOT* EDIT THIS FILE.  IT IS GENERATED.
# PLEASE UPDATE Dockerfile.erb INSTEAD OF THIS FILE
# !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
FROM php:7.4.7-buster

ENV GLOBAL_RUBY_VERSION 2.7.1
ENV BUNDLER1_VERSION 1.17.3
ENV BUNDLER2_VERSION 2.1.4
ENV LANG en_US.UTF-8
ENV LANGUAGE $LANG
ENV LC_ALL $LANG
ENV RUNNER_USER analyzer_runner
ENV RUNNER_GROUP nogroup
ENV RUNNER_USER_HOME /home/${RUNNER_USER}
ENV GEM_HOME ${RUNNER_USER_HOME}/ruby
ENV PATH ${RUNNER_USER_HOME}/bin:${GEM_HOME}/bin:${PATH}
ENV DEBIAN_FRONTEND noninteractive

COPY base/bin/prepare /tmp/devon_rex_work/
RUN /tmp/devon_rex_work/prepare

COPY --from=ruby:2.7.1 /usr/local /usr/local/

USER $RUNNER_USER

RUN gem update --system && \
    # NOTE: Install bundler v1 as well as v2 because runner applications need them
    #       in order to parse both versions of Gemfile.lock.
    #       See https://bunndler.io/blog/2019/01/03/announcing-bundler-2.html
    gem install bundler:"${BUNDLER1_VERSION}" && \
    gem install bundler:"${BUNDLER2_VERSION}" && \
    # Verify Bundler installation
    test "$(bundle -v)" = "Bundler version ${BUNDLER2_VERSION}" || (echo 'Unexpected Bundler version!' ; exit 1) && \
    gem info bundler && \
    gem environment && \
    ruby -v && gem -v && bundle -v


USER root

# PHP configuration
RUN mv $PHP_INI_DIR/php.ini-production $PHP_INI_DIR/php.ini
COPY php/sider.ini $PHP_INI_DIR/conf.d/sider.ini

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

ENV COMPOSER_HOME $RUNNER_USER_HOME/.composer
ENV COMPOSER_VENDOR_BIN $COMPOSER_HOME/vendor/bin
ENV PATH $COMPOSER_VENDOR_BIN:$PATH

USER $RUNNER_USER
RUN mkdir -p $COMPOSER_VENDOR_BIN

USER root
RUN rm -rf /tmp/devon_rex_work

# Following instructions should be run by a non-root user
USER $RUNNER_USER


CMD php -v && composer --version
