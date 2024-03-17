# Use Ubuntu as the base OS
FROM ubuntu:22.04

# Install dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    wget \
    git \
    curl \
    unzip \
    nano \
    neofetch \
    openjdk-17-jre \
    openjdk-17-jdk \
  && rm -rf /var/lib/apt/lists/*

# Create workspace directory
RUN mkdir /home/codespace
WORKDIR /home/codespace

# Set environment variables
ENV PUB_CACHE=/home/codespace/.pub_cache
ENV PATH="/home/codespace/flutter/bin:$PATH"
ENV ANDROID_HOME=/home/codespace/android
ENV PATH="$ANDROID_HOME/cmdline-tools/tools/bin/:$ANDROID_HOME/emulator/:$ANDROID_HOME/platform-tools/:$PATH"

# Download and install specific Flutter version
RUN git clone https://github.com/flutter/flutter.git
WORKDIR /home/codespace/flutter
RUN git checkout tags/3.19.0

# Enable web support
RUN ./bin/flutter config --enable-web

# Android SDK Setup
RUN mkdir /home/codespace/android
WORKDIR /home/codespace/android

# Download the latest commandline tools
RUN wget https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip

# Unzip and remove archive and move tools directory inside cmdline-tools
RUN unzip commandlinetools-linux-11076708_latest.zip \
    && rm commandlinetools-linux-11076708_latest.zip \
    && cd cmdline-tools \
    && mkdir tools \
    && ls | grep -v "tools" | xargs mv -t tools

# Install required Android SDK components
RUN yes | sdkmanager --licenses \
  && sdkmanager --install "platform-tools" "platforms;android-34" "build-tools;34.0.0"

# Clean up
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Reset work directory
WORKDIR /home/codespace
