FROM nvidia/opengl:1.2-glvnd-devel-ubuntu22.04

ENV NODE_VERSION v18.12.0
ENV NVM_DIR /usr/src/.nvm
ENV NODE_PATH $NVM_DIR/versions/node/$NODE_VERSION/bin
ENV PATH $NODE_PATH:$PATH
# Env vars for the nvidia-container-runtime.
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES all
ENV LSB_RELEASE=jammy

ARG DEBIAN_FRONTEND=noninteractive
ARG BLESS_USER_ID=999

COPY fonts.conf /etc/fonts/local.conf

# Update base image
RUN apt-get -qq update && \
  apt-get -qq dist-upgrade

RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        ca-certificates \
        build-essential \
        g++ \
        libxinerama-dev \
        libxext-dev \
        libxrandr-dev \
        libxi-dev \
        libxcursor-dev \
        libxxf86vm-dev \
        wget \
        gnupg \
        libvulkan-dev && \
    rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y apt-utils && apt-get install -y curl

# Install GTK, pulseaudio and fonts
RUN apt-get update && \
    apt-get -y --no-install-recommends install tzdata \
                       libcanberra-gtk-module libexif12 pulseaudio attr \
                       fonts-dejavu-core fonts-freefont-ttf fonts-guru-extra \
                       fonts-kacst fonts-kacst-one fonts-khmeros-core fonts-lao \
                       fonts-liberation fonts-lklug-sinhala fonts-lohit-guru \
                       fonts-nanum fonts-opensymbol fonts-sil-abyssinica \
                       fonts-sil-padauk fonts-symbola fonts-takao-pgothic \
                       fonts-tibetan-machine fonts-tlwg-garuda-ttf \
                       fonts-tlwg-kinnari-ttf fonts-tlwg-laksaman-ttf \
                       fonts-tlwg-loma-ttf fonts-tlwg-mono-ttf \
                       fonts-tlwg-norasi-ttf fonts-tlwg-purisa-ttf \
                       fonts-tlwg-sawasdee-ttf fonts-tlwg-typewriter-ttf \
                       fonts-tlwg-typist-ttf fonts-tlwg-typo-ttf \
                       fonts-tlwg-umpush-ttf fonts-tlwg-waree-ttf \
                       ttf-bitstream-vera \
                       fonts-arphic-ukai fonts-arphic-uming \
                       fonts-ipafont-mincho fonts-ipafont-gothic \
                       fonts-unfonts-core && \
    rm -rf -- /var/lib/apt/lists /tmp/*.deb

# Add the partner repository
RUN echo "deb http://archive.canonical.com/ubuntu $LSB_RELEASE partner" > /etc/apt/sources.list.d/ubuntu-partner.list \
  && apt update

# Accept Microsoft EULA agreement for ttf-mscorefonts-installer
RUN echo "ttf-mscorefonts-installer msttcorefonts/accepted-mscorefonts-eula select true" | debconf-set-selections

# Install dependencies for Chrome / Chromium
RUN apt-get -y -qq --no-install-recommends install \
  dumb-init \
  ffmpeg \
  fontconfig \
  fonts-freefont-ttf \
  fonts-gfs-neohellenic \
  fonts-indic \
  fonts-ipafont-gothic \
  fonts-kacst \
  fonts-liberation \
  fonts-noto-cjk \
  fonts-noto-color-emoji \
  fonts-roboto \
  fonts-thai-tlwg \
  fonts-ubuntu \
  fonts-wqy-zenhei \
  gconf-service \
  git \
  libappindicator1 \
  libappindicator3-1 \
  libasound2 \
  libatk-bridge2.0-0 \
  libatk1.0-0 \
  libc6 \
  libcairo2 \
  libcups2 \
  libdbus-1-3 \
  libexpat1 \
  libfontconfig1 \
  libgbm-dev \
  libgbm1 \
  libgcc1 \
  libgconf-2-4 \
  libgdk-pixbuf2.0-0 \
  libglib2.0-0 \
  libgtk-3-0 \
  libnspr4 \
  libnss3 \
  libpango-1.0-0 \
  libpangocairo-1.0-0 \
  libstdc++6 \
  libu2f-udev \
  libx11-6 \
  libx11-xcb1 \
  libxcb1 \
  libxcomposite1 \
  libxcursor1 \
  libxdamage1 \
  libxext6 \
  libxfixes3 \
  libxi6 \
  libxrandr2 \
  libxrender1 \
  libxss1 \
  libxtst6 \
  locales \
  lsb-release \
  msttcorefonts \
  pdftk \
  unzip \
  xdg-utils \
  xvfb

# Install nvm with node and npm
RUN mkdir -p $NVM_DIR &&\
  curl -sL https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash &&\
  . $NVM_DIR/nvm.sh &&\
  nvm install $NODE_VERSION

# Cleanup
RUN fc-cache -f -v && \
  apt-get -qq clean && \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Add the browserless user (blessuser)
RUN groupadd -r blessuser && useradd --uid ${BLESS_USER_ID} -r -g blessuser -G audio,video blessuser && \
  mkdir -p /home/blessuser/Downloads && \
  chown -R blessuser:blessuser /home/blessuser