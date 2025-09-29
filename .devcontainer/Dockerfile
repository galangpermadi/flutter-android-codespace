FROM gitpod/workspace-full-vnc
SHELL ["/bin/bash", "-c"]

# Set JAVA_HOME
ENV JAVA_HOME="$HOME/.sdkman/candidates/java/current"

# Install java 17 via sdkman
RUN source "$HOME/.sdkman/bin/sdkman-init.sh" \
    && sdk install java 17.0.11.fx-zulu \
    && sdk default java 17.0.11.fx-zulu

ENV ANDROID_HOME=$HOME/androidsdk \
    FLUTTER_VERSION=3.19.6-stable \
    QTWEBENGINE_DISABLE_SANDBOX=1
ENV PATH="$HOME/flutter/bin:$ANDROID_HOME/emulator:$ANDROID_HOME/tools:$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH"

# Install Open JDK for android and other dependencies
USER root
# install chrome
RUN apt-get install -y wget
RUN wget -q https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
RUN apt-get install -y ./google-chrome-stable_current_amd64.deb

RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
&& unzip awscliv2.zip \
&& ./aws/install

# Make some changes for our vnc client and flutter chrome
# RUN sed -i 's|resize=scale|resize=remote|g' /opt/novnc/index.html \
#     && _gc_path="$(command -v google-chrome)" \
#     && rm "$_gc_path" && printf '%s\n' '#!/usr/bin/env bash' \
#                                         'chromium --start-fullscreen "$@"' > "$_gc_path" \
#     && chmod +x "$_gc_path" 

# Insall flutter and dependencies
USER gitpod
RUN npm uninstall -g pnpm && npm install -g pnpm@8.15.4
RUN npm i -g @ionic/cli
RUN npm i -g firebase-tools

RUN wget -q "https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_${FLUTTER_VERSION}.tar.xz" -O - \
    | tar xpJ -C "$HOME" \
    && _file_name="commandlinetools-linux-8092744_latest.zip" && wget "https://dl.google.com/android/repository/$_file_name" \
    && unzip "$_file_name" -d $ANDROID_HOME \
    && rm -f "$_file_name" \
    && mkdir -p $ANDROID_HOME/cmdline-tools/latest \
    && mv $ANDROID_HOME/cmdline-tools/{bin,lib} $ANDROID_HOME/cmdline-tools/latest \
    && yes | sdkmanager "platform-tools" "build-tools;31.0.0" "platforms;android-31" \
    && flutter precache && for _plat in web linux-desktop; do flutter config --enable-${_plat}; done \
    && flutter config --android-sdk $ANDROID_HOME \
    && yes | flutter doctor --android-licenses \
    && flutter doctor
