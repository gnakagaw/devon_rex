# !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
# NOTE: DO *NOT* EDIT THIS FILE.  IT IS GENERATED.
# PLEASE UPDATE Dockerfile.erb INSTEAD OF THIS FILE
# !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
FROM debian:buster-20191014

ENV GLOBAL_RUBY_VERSION 2.6.5
ENV BUNDLER1_VERSION 1.17.3
ENV BUNDLER2_VERSION 2.0.2
ENV DEVON_REX_RBENV_VERSION v1.1.2
ENV DEVON_REX_RUBY_BUILD_VERSION v20191105
ENV LANG en_US.UTF-8
ENV LANGUAGE $LANG
ENV LC_ALL $LANG
ENV RUNNER_USER analyzer_runner
ENV RUNNER_USER_HOME /home/$RUNNER_USER
ENV DEBIAN_FRONTEND=noninteractive

COPY base/bin/* /tmp/devon_rex_work/
RUN /tmp/devon_rex_work/prepare


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
RUN curl -fsSLO --compressed "${MAVEN_BINARY_URL}" && \
    curl -fsSL  --compressed "${MAVEN_BINARY_URL}.sha512" | sha512sum --check --strict && \
    mkdir -p /usr/local/maven && \
    tar -xzf "apache-maven-${MAVEN_VERSION}-bin.tar.gz" -C /usr/local/maven --strip-components 1 && \
    rm "apache-maven-${MAVEN_VERSION}-bin.tar.gz"

# Following instructions should be run by $RUNNER_USER
USER $RUNNER_USER

RUN /tmp/devon_rex_work/build-ruby
ENV PATH ${RUNNER_USER_HOME}/.rbenv/shims:${RUNNER_USER_HOME}/.rbenv/bin:${PATH}


CMD java -version && javac -version && mvn -v
