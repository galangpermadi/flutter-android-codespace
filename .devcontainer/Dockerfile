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
    && rm -rf /var/lib/apt/lists/*

# Environment variables
ENV ANDROID_HOME=/usr/local/android-sdk
ENV PATH="${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/build-tools/34.0.0:${PATH}"
ENV GRADLE_HOME=/usr/local/gradle
ENV PATH="${GRADLE_HOME}/gradle-8.1/bin:${PATH}"

# Install Flutter
RUN git clone https://github.com/flutter/flutter.git /usr/local/flutter
ENV PATH="/usr/local/flutter/bin:${PATH}"

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

# Install AI libraries (Coqui, TensorFlow Lite, MediaPipe, OpenAI Whisper)
RUN pip3 install coqui-tts==0.11.0 openai-whisper tensorflow mediapipe

# Run Flutter doctor to verify installation
RUN flutter doctor

