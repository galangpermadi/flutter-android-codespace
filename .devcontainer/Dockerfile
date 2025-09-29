FROM mcr.microsoft.com/devcontainers/universal:latest

SHELL ["/bin/bash", "-c"]

# =============================
# 1. Install dependencies tambahan
# =============================
RUN sudo apt-get update && sudo apt-get install -y \
    libglu1-mesa \
    unzip xz-utils \
    && sudo rm -rf /var/lib/apt/lists/*

# =============================
# 2. Install Flutter & Android SDK
# =============================
USER codespace
WORKDIR /home/codespace

ENV ANDROID_HOME=/home/codespace/androidsdk \
    FLUTTER_VERSION=3.19.6-stable \
    QTWEBENGINE_DISABLE_SANDBOX=1
ENV PATH="/home/codespace/flutter/bin:$ANDROID_HOME/emulator:$ANDROID_HOME/tools:$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:${PATH}"

RUN wget -q "https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_${FLUTTER_VERSION}.tar.xz" -O - \
    | tar xpJ -C "/home/codespace" \
    && _file_name="commandlinetools-linux-8092744_latest.zip" \
    && wget "https://dl.google.com/android/repository/$_file_name" \
    && unzip "$_file_name" -d $ANDROID_HOME \
    && rm -f "$_file_name" \
    && mkdir -p $ANDROID_HOME/cmdline-tools/latest \
    && mv $ANDROID_HOME/cmdline-tools/{bin,lib} $ANDROID_HOME/cmdline-tools/latest \
    && yes | sdkmanager "platform-tools" "build-tools;31.0.0" "platforms;android-31" \
    && flutter precache \
    && for _plat in web linux-desktop; do flutter config --enable-${_plat}; done \
    && flutter config --android-sdk $ANDROID_HOME \
    && yes | flutter doctor --android-licenses \
    && flutter doctor

# =============================
# 3. Install Gradle (versi terbaru)
# =============================
ENV GRADLE_HOME=/opt/gradle
ENV PATH=${GRADLE_HOME}/bin:${PATH}
RUN wget -q https://services.gradle.org/distributions/gradle-8.10.2-bin.zip -O gradle.zip \
    && sudo unzip -d /opt gradle.zip \
    && sudo mv /opt/gradle-* /opt/gradle \
    && rm gradle.zip
