FROM mcr.microsoft.com/devcontainers/universal:latest

SHELL ["/bin/bash", "-c"]

# =====================================================
# 1. Install dependencies
# =====================================================
RUN sudo apt-get update && sudo apt-get install -y \
    libglu1-mesa \
    unzip \
    xz-utils \
    wget \
    curl \
    zip \
    git \
    && sudo apt-get clean \
    && sudo rm -rf /var/lib/apt/lists/*

# =====================================================
# 2. User
# =====================================================
USER codespace
WORKDIR /home/codespace

# =====================================================
# 3. Java (SDKMAN)
# =====================================================

# Ganti versi jika sudah tidak tersedia:
# RUN bash -lc "source $SDKMAN_DIR/bin/sdkman-init.sh && sdk list java"

ENV SDKMAN_DIR=/usr/local/sdkman

RUN bash -lc "\
    source ${SDKMAN_DIR}/bin/sdkman-init.sh && \
    sdk install java 21.0.8-tem && \
    sdk default java 21.0.8-tem"

ENV JAVA_HOME=${SDKMAN_DIR}/candidates/java/current

# =====================================================
# 4. Flutter & Android SDK
# =====================================================

ENV FLUTTER_VERSION=3.44.4-stable
ENV ANDROID_HOME=/home/codespace/androidsdk
ENV ANDROID_SDK_ROOT=/home/codespace/androidsdk
ENV QTWEBENGINE_DISABLE_SANDBOX=1

ENV PATH="${JAVA_HOME}/bin:/home/codespace/flutter/bin:${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/emulator:${PATH}"

# Flutter
RUN wget -q "https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_${FLUTTER_VERSION}.tar.xz" -O - \
    | tar -xJ -C /home/codespace

# Android Command Line Tools
RUN mkdir -p ${ANDROID_HOME}/cmdline-tools \
    && wget -q https://dl.google.com/android/repository/commandlinetools-linux-14742923_latest.zip -O cmdline-tools.zip \
    && unzip -q cmdline-tools.zip -d ${ANDROID_HOME}/cmdline-tools \
    && mv ${ANDROID_HOME}/cmdline-tools/cmdline-tools ${ANDROID_HOME}/cmdline-tools/latest \
    && rm cmdline-tools.zip

# Accept Licenses
RUN yes | ${ANDROID_HOME}/cmdline-tools/latest/bin/sdkmanager \
    --sdk_root=${ANDROID_HOME} \
    --licenses

# Install Android SDK
RUN ${ANDROID_HOME}/cmdline-tools/latest/bin/sdkmanager \
    --sdk_root=${ANDROID_HOME} \
    "platform-tools" \
    "platforms;android-36" \
    "build-tools;36.0.0"

# Flutter setup
RUN flutter config --android-sdk ${ANDROID_HOME} \
    && flutter config --enable-web \
    && flutter config --enable-linux-desktop \
    && flutter precache \
    && flutter doctor -v

# =====================================================
# 5. Gradle (Optional)
# Flutter build tetap memakai gradlew project
# =====================================================

ENV GRADLE_HOME=/opt/gradle
ENV PATH="${GRADLE_HOME}/bin:${PATH}"

RUN wget -q https://services.gradle.org/distributions/gradle-8.14-bin.zip -O gradle.zip \
    && sudo unzip -q gradle.zip -d /opt \
    && sudo mv /opt/gradle-8.14 /opt/gradle \
    && rm gradle.zip

# =====================================================
# 6. Verify
# =====================================================

RUN bash -lc "\
    source ${SDKMAN_DIR}/bin/sdkman-init.sh && \
    echo JAVA_HOME=\$JAVA_HOME && \
    java -version && \
    sdk current java"

RUN flutter --version

RUN flutter doctor -v

RUN gradle -v

RUN ${ANDROID_HOME}/cmdline-tools/latest/bin/sdkmanager \
    --sdk_root=${ANDROID_HOME} \
    --list | head -20
