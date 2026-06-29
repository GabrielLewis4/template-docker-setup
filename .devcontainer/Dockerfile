FROM node:lts

# Prevent interactive prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Install system dependencies.
# The lib* packages are C libraries required to compile R packages from source.
RUN apt-get update && apt-get install -y \
    r-base \
    r-base-dev \
    python3 \
    python3-pip \
    curl \
    wget \
    git \
    openssh-client \
    build-essential \
    libssl-dev \
    libcurl4-openssl-dev \
    libxml2-dev \
    libfontconfig1-dev \
    libharfbuzz-dev \
    libfribidi-dev \
    libfreetype6-dev \
    libpng-dev \
    libtiff5-dev \
    libjpeg-dev \
    && rm -rf /var/lib/apt/lists/*

# Install Claude Code
RUN npm install -g @anthropic-ai/claude-code --allow-scripts

# Install uv (Python package manager)
RUN pip install uv --break-system-packages

# Install Quarto (detects architecture automatically, works on Apple Silicon and Intel)
RUN ARCH=$(uname -m | sed 's/x86_64/amd64/;s/aarch64/arm64/') && \
    VERSION=$(curl -s "https://api.github.com/repos/quarto-dev/quarto-cli/releases/latest" | \
              grep '"tag_name"' | sed 's/.*"v\(.*\)".*/\1/') && \
    curl -fsSL "https://github.com/quarto-dev/quarto-cli/releases/download/v${VERSION}/quarto-${VERSION}-linux-${ARCH}.deb" \
    -o quarto.deb && \
    dpkg -i quarto.deb && \
    rm quarto.deb

# Install R packages.
# This is the slowest step — packages compile from source and may take 20-30 minutes.
# It only happens once; Docker caches the result.
RUN Rscript -e "install.packages(c( \
    'tidyverse', \
    'here', \
    'brms', \
    'cmdstanr', \
    'arrow', \
    'duckdb', \
    'quarto', \
    'rmarkdown', \
    'patchwork', \
    'janitor', \
    'skimr', \
    'renv' \
    ), repos='https://cloud.r-project.org')"
