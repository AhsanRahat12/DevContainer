FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

# Copy the mise binary directly from the official mise image into our container
COPY --from=jdxcode/mise /usr/local/bin/mise /usr/local/bin/

# Activate mise for both bash and zsh so it's available in any new shell
RUN echo 'eval "$(mise activate bash)"' >> /home/vscode/.bashrc && \
    echo 'eval "$(mise activate zsh)"' >> /home/vscode/.zshrc
