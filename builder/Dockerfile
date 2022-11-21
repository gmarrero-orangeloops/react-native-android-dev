FROM ubuntu:focal AS development

ARG JAVA_VERSION=11
ARG NODEJS_VERSION=16
ARG ANDROID_VERSION=31
ARG GRADLE_VERSION=7.5.1
ARG WATCHMAM_VERSION=v2022.11.14.00

ADD . /code
WORKDIR /code

RUN apt update
RUN echo y | apt upgrade
RUN apt install -y curl
RUN apt install -y wget
RUN apt install -y unzip

# JAVA install
RUN echo 1 | apt install -y openjdk-${JAVA_VERSION}-jdk-headless

ENV JAVA_HOME /usr/lib/jvm/java-${JAVA_VERSION}-openjdk-amd64
ENV PATH $PATH:$JAVA_HOME/bin:$JAVA_HOME

# Node.js install
RUN curl -fsSL https://deb.nodesource.com/setup_${NODEJS_VERSION}.x | bash - \
    && apt install -y nodejs

# Android install
RUN apt install -y android-tools-adb

ENV ANDROID_HOME /opt/android

RUN mkdir -p ${ANDROID_HOME} && cd ${ANDROID_HOME} \
    && wget -q https://dl.google.com/android/repository/commandlinetools-linux-9123335_latest.zip -O commandlinetools.zip \
    && unzip -q commandlinetools.zip \
    && rm -f commandlinetools.zip \
    && cd ${ANDROID_HOME}/cmdline-tools \
    && mkdir ../latest && mv * ../latest && mv ../latest/ .

ENV ANDROID_TOOLS /opt/android/cmdline-tools/latest/bin
ENV ANDROID_SDK_ROOT $ANDROID_HOME
ENV PATH $PATH:$ANDROID_TOOLS:$ANDROID_SDK_ROOT/emulator:$ANDROID_SDK_ROOT/platform-tools

RUN echo y | sdkmanager "build-tools;${ANDROID_VERSION}.0.0"
RUN sdkmanager "platforms;android-${ANDROID_VERSION}"

# Gradle install
RUN wget https://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip -P /tmp \
    && unzip -d /opt/gradle /tmp/gradle-${GRADLE_VERSION}-bin.zip \
    && ln -s /opt/gradle/gradle-${GRADLE_VERSION} /opt/gradle/latest \
    && export GRADLE_HOME=/opt/gradle/latest \
    && export PATH=$PATH:$GRADLE_HOME/bin

# Watchman install
RUN wget https://github.com/facebook/watchman/releases/download/$WATCHMAM_VERSION/watchman_ubuntu20.04_$WATCHMAM_VERSION.deb \
    && echo Y | apt install ./watchman_ubuntu20.04_$WATCHMAM_VERSION.deb \
    && rm ./watchman_ubuntu20.04_$WATCHMAM_VERSION.deb


RUN npm install
CMD ["node", "server.js"]


