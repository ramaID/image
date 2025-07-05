# Runtime stage
FROM debian:bookworm-slim

# Install runtime dependencies
RUN apt-get update && apt-get install -y \
    imagemagick \
    ffmpeg \
    libreoffice \
    unoconv \
    curl \
    ca-certificates \
    libssl3 \
    poppler-utils \
    && rm -rf /var/lib/apt/lists/*

# Fix ImageMagick security policy for PDF processing
RUN sed -i 's/<policy domain="coder" rights="none" pattern="PDF" \/>/<policy domain="coder" rights="read|write" pattern="PDF" \/>/g' /etc/ImageMagick-6/policy.xml || \
    sed -i 's/<policy domain="coder" rights="none" pattern="PDF" \/>/<policy domain="coder" rights="read|write" pattern="PDF" \/>/g' /etc/ImageMagick-7/policy.xml || \
    echo "ImageMagick policy file not found, skipping PDF policy fix"

# Create a non-root user with home directory
RUN useradd -r -m -s /bin/bash filelens

# Create app directory
WORKDIR /app

# Create necessary directories and set permissions
RUN mkdir -p /tmp/filelens /home/filelens/.cache /home/filelens/.config && \
    chown -R filelens:filelens /tmp/filelens /home/filelens /app && \
    chmod 755 /home/filelens && \
    chmod 755 /home/filelens/.cache && \
    chmod 755 /home/filelens/.config

# Set up LibreOffice user profile directory to avoid permission issues
RUN mkdir -p /home/filelens/.config/libreoffice && \
    chown -R filelens:filelens /home/filelens/.config/libreoffice

# Switch to non-root user
USER filelens

# Set environment variables
ENV RUST_LOG=filelens=info
ENV PORT=3000
ENV BIND_ADDRESS=0.0.0.0
ENV HOME=/home/filelens

# Expose port
EXPOSE 3000
