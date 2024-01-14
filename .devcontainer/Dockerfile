FROM python:bullseye

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update \
    && apt-get -y install --no-install-recommends apt-utils 2>&1

# Verify git and needed tools are installed
RUN apt-get install --no-install-recommends -y git procps

# Install Tex Live
RUN apt-get update && apt-get -y upgrade \
    && apt-get -y install --no-install-recommends \
    texlive-latex-base \
    texlive-extra-utils \
    texlive-latex-extra \
    biber chktex latexmk make python3-pygments python3-pkg-resources \
    texlive-lang-chinese \
    texlive-lang-japanese

# latexindent modules
RUN apt-get install --no-install-recommends -y curl
RUN curl -L http://cpanmin.us | perl - App::cpanminus \
    && cpanm Log::Dispatch::File \
    && cpanm YAML::Tiny \
    && cpanm File::HomeDir \
    && cpanm Unicode::GCString

# Timezone
RUN apt-get update && apt-get -y install --no-install-recommends tzdata \
    && echo "Europe/Vienna" > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata

# Quarto
ENV QUARTO_VERSION=1.3.450
RUN if [ $(uname -m) = "aarch64" ]; then ARCH=arm64; else ARCH=amd64; fi \
    && curl -LJO https://github.com/quarto-dev/quarto-cli/releases/download/v$QUARTO_VERSION/quarto-$QUARTO_VERSION-linux-$ARCH.deb \
    && dpkg -i quarto-$QUARTO_VERSION-linux-$ARCH.deb || apt-get install -f -y \
    && rm quarto-$QUARTO_VERSION-linux-$ARCH.deb \
    && echo "$QUARTO_VERSION"

# TinyTeX
RUN wget -qO- "https://yihui.org/tinytex/install-bin-unix.sh" | sh

# Native dependency for h5py
RUN apt-get update && apt-get install -y libhdf5-dev

# Python packages
RUN pip install h5py jupyter notebook jupyterlab matplotlib scipy numpy pandas altair tqdm vl-convert-python scikit-learn ruff

# Clean up
RUN apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf ~/.cache/pip
ENV DEBIAN_FRONTEND=dialog \
    LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    TZ=Europe/Vienna
