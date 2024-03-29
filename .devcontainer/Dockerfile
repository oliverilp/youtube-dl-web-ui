# File based on:
# https://github.com/devcontainers/images/blob/main/src/javascript-node/.devcontainer/Dockerfile
ARG VARIANT=20-bookworm
FROM node:${VARIANT}

ARG USERNAME=node
ARG NPM_GLOBAL=/usr/local/share/npm-global

# Add NPM global to PATH.
ENV PATH=${NPM_GLOBAL}/bin:${PATH}

# Configure global npm install location, use group to adapt to UID/GID changes
RUN if ! cat /etc/group | grep -e "^npm:" > /dev/null 2>&1; then groupadd -r npm; fi \
    && usermod -a -G npm ${USERNAME} \
    && umask 0002 \
    && mkdir -p ${NPM_GLOBAL} \
    && touch /usr/local/etc/npmrc \
    && chown ${USERNAME}:npm ${NPM_GLOBAL} /usr/local/etc/npmrc \
    && chmod g+s ${NPM_GLOBAL} \
    && npm config -g set prefix ${NPM_GLOBAL} \
    && su ${USERNAME} -c "npm config -g set prefix ${NPM_GLOBAL}" \
    # Install eslint
    && su ${USERNAME} -c "umask 0002 && npm install -g eslint" \
    && npm cache clean --force > /dev/null 2>&1

# Install base dependencies
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends xattr \
    python3-minimal python-is-python3 python3-pip \
    python3-certifi python3-websockets python3-requests python3-brotli \
    python3-mutagen ffmpeg atomicparsley \
    && apt autoremove -y --purge \
    && apt clean \
    && rm -rf /var/lib/apt/lists/*

# Install youtube-dl
RUN mkdir -p /home/node/.local/bin \
    && curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o /home/node/.local/bin/yt-dlp \
    && chmod a+rx /home/node/.local/bin/yt-dlp  # Make executable \
    && ln -s /home/node/.local/bin/yt-dlp /usr/local/bin/yt-dlp 
