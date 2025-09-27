# Base image
FROM ubuntu:22.04

# Install essential tools
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    wget \
    git \
    curl \
    unzip \
    nano \
    python3-pip \
    libglu1-mesa \
    openjdk-17-jre \
    openjdk-17-jdk \
    xz-utils \
    zip \
    && rm -rf /var/lib/apt/lists/*

# Environment variables
ENV ANDROID_HOME=/usr/local/android-sdk
ENV PATH="${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/build-tools/34.0.0:${PATH}"
ENV GRADLE_HOME=/usr/local/gradle
ENV PATH="${GRADLE_HOME}/gradle-8.1/bin:${PATH}"
ENV PATH="/usr/local/flutter/bin:/usr/local/flutter/bin/cache/dart-sdk/bin:${PATH}"

# Install Flutter (stable branch)
RUN git clone https://github.com/flutter/flutter.git /usr/local/flutter -b stable --depth 1

# Install Gradle
RUN wget https://services.gradle.org/distributions/gradle-8.1-bin.zip && \
    unzip gradle-8.1-bin.zip -d /usr/local/gradle && \
    rm gradle-8.1-bin.zip

# Install Android SDK
RUN mkdir -p ${ANDROID_HOME}/cmdline-tools && \
    wget https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip -O /tmp/cmdline-tools.zip && \
    unzip /tmp/cmdline-tools.zip -d ${ANDROID_HOME}/cmdline-tools && \
    rm /tmp/cmdline-tools.zip && \
    mv ${ANDROID_HOME}/cmdline-tools/cmdline-tools ${ANDROID_HOME}/cmdline-tools/latest && \
    yes | sdkmanager --licenses && \
    sdkmanager "platform-tools" "platforms;android-34" "build-tools;34.0.0"

# Create non-root user for Flutter
RUN useradd -ms /bin/bash flutteruser
USER flutteruser
WORKDIR /home/flutteruser

# Clear old cache and run Flutter doctor
RUN rm -rf /usr/local/flutter/bin/cache && flutter precache
RUN flutter doctor
