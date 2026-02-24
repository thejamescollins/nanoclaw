# NanoClaw Agent Container
# Runs Claude Agent SDK in isolated Linux VM with browser automation

FROM node:22-slim

# Install system dependencies for Chromium
RUN apt-get update && apt-get install -y \
    chromium \
    fonts-liberation \
    fonts-noto-color-emoji \
    libgbm1 \
    libnss3 \
    libatk-bridge2.0-0 \
    libgtk-3-0 \
    libx11-xcb1 \
    libxcomposite1 \
    libxdamage1 \
    libxrandr2 \
    libasound2 \
    libpangocairo-1.0-0 \
    libcups2 \
    libdrm2 \
    libxshmfence1 \
    curl \
    git \
    # GitHub CLI — for creating PRs, viewing issues, repo operations
    && curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg \
       | dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
       > /etc/apt/sources.list.d/github-cli.list \
    && apt-get update && apt-get install -y gh \
    && rm -rf /var/lib/apt/lists/* \
    # PHP 8.4 + extensions for Laravel projects (wine-club etc.)
    # sqlite3 needed for test suite, mysql for production driver, bcmath/gd/intl common Laravel deps
    && curl -sSL https://packages.sury.org/php/README.txt | bash -x \
    && apt-get update && apt-get install -y \
       php8.4-cli php8.4-mbstring php8.4-xml php8.4-curl php8.4-zip \
       php8.4-sqlite3 php8.4-mysql php8.4-bcmath php8.4-gd php8.4-intl \
       php8.4-readline php8.4-dom php8.4-tokenizer \
       unzip \
    && rm -rf /var/lib/apt/lists/* \
    # Composer — PHP dependency manager
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Set Chromium path for agent-browser
ENV AGENT_BROWSER_EXECUTABLE_PATH=/usr/bin/chromium
ENV PLAYWRIGHT_CHROMIUM_EXECUTABLE_PATH=/usr/bin/chromium

# Install agent-browser and claude-code globally
RUN npm install -g agent-browser @anthropic-ai/claude-code counselors

# Install Chief — autonomous PRD agent (Go binary)
RUN CHIEF_VERSION=$(curl -fsSL https://api.github.com/repos/minicodemonkey/chief/releases/latest | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/') \
    && ARCH=$(uname -m | sed 's/x86_64/amd64/' | sed 's/aarch64/arm64/') \
    && curl -fsSL "https://github.com/minicodemonkey/chief/releases/download/${CHIEF_VERSION}/chief_${CHIEF_VERSION#v}_linux_${ARCH}.tar.gz" \
       | tar -xz -C /usr/local/bin chief

# Create app directory
WORKDIR /app

# Copy package files first for better caching
COPY agent-runner/package*.json ./

# Install dependencies
RUN npm install

# Copy source code
COPY agent-runner/ ./

# Build TypeScript
RUN npm run build

# Create workspace directories
RUN mkdir -p /workspace/group /workspace/global /workspace/extra /workspace/ipc/messages /workspace/ipc/tasks /workspace/ipc/input

# Create entrypoint script
# Secrets are passed via stdin JSON — temp file is deleted immediately after Node reads it
# Follow-up messages arrive via IPC files in /workspace/ipc/input/
RUN printf '#!/bin/bash\nset -e\ncd /app && npx tsc --outDir /tmp/dist 2>&1 >&2\nln -s /app/node_modules /tmp/dist/node_modules\nchmod -R a-w /tmp/dist\ncat > /tmp/input.json\nnode /tmp/dist/index.js < /tmp/input.json\n' > /app/entrypoint.sh && chmod +x /app/entrypoint.sh

# Set ownership to node user (non-root) for writable directories
RUN chown -R node:node /workspace && chmod 777 /home/node

# Git identity for commits (tectalic-lex GitHub account)
# Credential helper uses GITHUB_TOKEN env var for HTTPS push/pull auth
RUN git config --system user.name "tectalic-lex" \
    && git config --system user.email "james+lex@tectalic.com" \
    && git config --system credential.helper '!f() { echo "username=x-access-token"; echo "password=$GITHUB_TOKEN"; }; f'

# Switch to non-root user (required for --dangerously-skip-permissions)
USER node

# Set working directory to group workspace
WORKDIR /workspace/group

# Entry point reads JSON from stdin, outputs JSON to stdout
ENTRYPOINT ["/app/entrypoint.sh"]
