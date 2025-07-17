# FileLens Infrastructure Docker Image
# High-performance file preview generation service base image
#
# Dependencies included:
# - LibreOffice: Document conversion (DOC, DOCX, PPT, PPTX, XLS, XLSX)
# - ImageMagick: Image processing and PDF manipulation (convert, identify)
# - FFmpeg: Video thumbnail generation
# - Pandoc: Enhanced document conversion with better formatting preservation
# - wkhtmltopdf: HTML to PDF conversion for Pandoc (preserves complex layouts)
# - Poppler Utils: PDF information extraction (pdfinfo)
# - unoconv: Alternative document converter
# - LaTeX: Enhanced PDF generation capabilities
# - Multiple font packages: Better text rendering
#
# Usage:
#   FROM ramaid/image:filelens-v2.7.2
#   # Add your application code here

FROM debian:bookworm-slim

# Install all required dependencies for FileLens
RUN apt-get update && apt-get install -y \
    # Core document processing
    libreoffice \
    libreoffice-writer \
    libreoffice-calc \
    libreoffice-impress \
    unoconv \
    # Image processing (ImageMagick)
    imagemagick \
    # Video processing
    ffmpeg \
    # PDF utilities
    poppler-utils \
    # Network utilities
    curl \
    wget \
    # Better document conversion with numbering lists preservation
    pandoc \
    pandoc-data \
    # Additional tools for downloading wkhtmltopdf
    xfonts-75dpi \
    xfonts-base \
    # LaTeX for better PDF generation
    texlive-latex-recommended \
    texlive-fonts-recommended \
    texlive-latex-extra \
    texlive-xetex \
    texlive-base \
    texlive-binaries \
    # Additional fonts for better rendering
    fonts-liberation \
    fonts-dejavu \
    fonts-noto \
    # System utilities
    ca-certificates \
    libssl3 \
    && rm -rf /var/lib/apt/lists/*

# Install wkhtmltopdf for Debian Bookworm with multiple fallback methods
RUN ( \
    # Method 1: Try Debian Bookworm package
    wget -q https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6.1-3/wkhtmltox_0.12.6.1-3.bookworm_amd64.deb && \
    dpkg -i wkhtmltox_0.12.6.1-3.bookworm_amd64.deb && \
    rm -f wkhtmltox_0.12.6.1-3.bookworm_amd64.deb \
    ) || ( \
    # Method 2: Try Bullseye (compatible with Bookworm)
    wget -q https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6.1-3/wkhtmltox_0.12.6.1-3.bullseye_amd64.deb && \
    dpkg -i wkhtmltox_0.12.6.1-3.bullseye_amd64.deb && \
    rm -f wkhtmltox_0.12.6.1-3.bullseye_amd64.deb \
    ) || ( \
    # Method 3: Install from static build (most compatible)
    wget -q https://downloads.wkhtmltopdf.org/0.12/0.12.5/wkhtmltox_0.12.5-1.buster_amd64.deb && \
    dpkg -i wkhtmltox_0.12.5-1.buster_amd64.deb && \
    rm -f wkhtmltox_0.12.5-1.buster_amd64.deb \
    ) || echo "wkhtmltopdf installation failed - will use Pandoc fallback"

# Fix any dependency issues from wkhtmltopdf installation
RUN apt-get update && apt-get install -f -y || true

# Fix ImageMagick security policy for PDF processing
RUN sed -i 's/<policy domain="coder" rights="none" pattern="PDF" \/>/<policy domain="coder" rights="read|write" pattern="PDF" \/>/g' /etc/ImageMagick-6/policy.xml || true

# Verify all tools are installed and working
RUN libreoffice --version && \
    convert --version && \
    identify --version && \
    ffmpeg -version && \
    pandoc --version && \
    unoconv --version && \
    pdfinfo -v && \
    curl --version && \
    xelatex --version && \
    (wkhtmltopdf --version || echo "wkhtmltopdf not available - Pandoc will use fallback engine")

# Create a non-root user with home directory (if not exists)
RUN id -u filelens >/dev/null 2>&1 || useradd -m -u 1000 -s /bin/bash filelens

# Create app directory
WORKDIR /app

# Create necessary directories and set proper permissions
RUN mkdir -p /tmp /tmp/filelens /var/cache/filelens /home/filelens/.cache/dconf && \
    chown -R filelens:filelens /tmp /var/cache/filelens /home/filelens

# Set up LibreOffice user profile directory to avoid permission issues
RUN mkdir -p /home/filelens/.config/libreoffice && \
    chown -R filelens:filelens /home/filelens/.config/libreoffice

# Tool paths (as defined in config.env.example)
ENV LIBREOFFICE_PATH=/usr/bin/libreoffice
ENV IMAGEMAGICK_PATH=/usr/bin/convert
ENV FFMPEG_PATH=/usr/bin/ffmpeg

# Disable dconf to prevent permission warnings
ENV GSETTINGS_BACKEND=memory

# FileLens configuration
ENV TEMP_DIR=/tmp/filelens
ENV CACHE_DIR=/var/cache/filelens
ENV MAX_FILE_SIZE=100MB
ENV CLEANUP_INTERVAL=3600
ENV ENABLE_ASYNC_PROCESSING=true

# Default to filelens user for application usage
USER filelens

# Default working directory for applications
WORKDIR /app
