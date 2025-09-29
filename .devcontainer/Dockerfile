FROM ubuntu:22.04
SHELL ["/bin/bash", "-c"]

# =============================
# 1. Install dependencies dasar
# =============================
RUN apt-get update && apt-get install -y \
    curl wget unzip git zip xz-utils sudo \
    build-essential ca-certificates gnupg lsb-release \
    openjdk-17-jdk \
    libglu1-mesa \
    software-properties-common \
    && rm -rf /var/lib/apt/lists/*

# =============================
# 2. Set JAVA_HOME
# =============================
ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64

# =============================
# 3. Install Chrome
# =============================
# RUN wget -q https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb \
#     && apt-get install -y ./google-chrome-stable_current_amd64.deb \
#     && rm google-chrome-stable_current_amd64.deb

# =============================
# 4. Install AWS CLI v2
# =============================
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
    && unzip awscliv2.zip \
    && ./aws/install \
    && rm -rf awscliv2.zip aws

# =============================
# 5. Install Node.js & global tools
# =============================
RUN curl -fsSL https://deb.nodesource.com/setup_lts.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g pnpm@8.15.4 @ionic/cli firebase-tools

# =============================
# 6. Buat user non-root "developer"
# =============================
RUN useradd -ms /bin/bash developer && \
    echo "developer ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
USER developer
WORKDIR /home/developer

# =============================
# 7. Install Flutter & Android SDK
# =============================
ENV ANDROID_HOME=/home/developer/androidsdk \
    FLUTTER_VERSION=3.19.6-stable \
    QTWEBENGINE_DISABLE_SANDBOX=1
ENV PATH="/home/developer/flutter/bin:$ANDROID_HOME/emulator:$ANDROID_HOME/tools:$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH"

RUN wget -q "https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_${FLUTTER_VERSION}.tar.xz" -O - \
    | tar xpJ -C "/home/developer" \
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
