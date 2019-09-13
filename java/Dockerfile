# !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
# NOTE: DO *NOT* EDIT THIS FILE.  IT IS GENERATED.
# PLEASE UPDATE Dockerfile.erb INSTEAD OF THIS FILE
# !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
FROM debian:buster-20190910

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


# Install Java
ENV JAVA_VERSION 12.0.1
ENV PATH /usr/local/java/bin:$PATH
ARG TAR_URL=https://download.java.net/java/GA/jdk${JAVA_VERSION}/69cfe15208a647278a19ef0990eea691/12/GPL/openjdk-${JAVA_VERSION}_linux-x64_bin.tar.gz
ARG SHA256_URL=https://download.java.net/java/GA/jdk${JAVA_VERSION}/69cfe15208a647278a19ef0990eea691/12/GPL/openjdk-${JAVA_VERSION}_linux-x64_bin.tar.gz.sha256
RUN curl -fsSLO --compressed $TAR_URL && \
    curl -fsSL -o sha256.txt --compressed $SHA256_URL && \
    echo "$(cat sha256.txt) *openjdk-${JAVA_VERSION}_linux-x64_bin.tar.gz" | sha256sum --check --strict && \
    mkdir -p /usr/local/java && \
    tar -xzf "openjdk-${JAVA_VERSION}_linux-x64_bin.tar.gz" -C /usr/local/java --strip-components=1 && \
    rm -rf "openjdk-${JAVA_VERSION}_linux-x64_bin.tar.gz" sha256.txt

# Install Maven
ENV MAVEN_VERSION 3.6.2
ENV PATH /usr/local/maven/bin:$PATH
ARG MAVEN_BINARY_URL=https://www.apache.org/dist/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz
RUN curl -fsSL --compressed "${MAVEN_BINARY_URL}" -o maven-bin.tar.gz && \
    curl -fsSL --compressed "${MAVEN_BINARY_URL}.sha512" | xargs -I {} echo "{} *maven-bin.tar.gz" | sha512sum --check --strict && \
    mkdir -p /usr/local/maven && \
    tar -xzf maven-bin.tar.gz -C /usr/local/maven --strip-components 1 && \
    rm maven-bin.tar.gz

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


CMD java -version && javac -version && mvn -v
