# !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
# NOTE: DO *NOT* EDIT THIS FILE.  IT IS GENERATED.
# PLEASE UPDATE Dockerfile.erb INSTEAD OF THIS FILE
# !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
FROM openjdk:13-jdk-buster

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

COPY base/bin/* /tmp/devon_rex_work/
RUN /tmp/devon_rex_work/prepare


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

RUN /tmp/devon_rex_work/build-ruby
ENV PATH ${RUNNER_USER_HOME}/.rbenv/shims:${RUNNER_USER_HOME}/.rbenv/bin:${PATH}


CMD java -version && javac -version && mvn -v
