FROM mcr.microsoft.com/devcontainers/javascript-node:1-18-bullseye

# >>> SPEEDUP APT zh-CN
RUN test -f /etc/apt/sources.list && \
    sed -i 's/deb.debian.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list ; \
    sed -i 's/security.debian.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list ; \
    exit 0

## Compatible with DEB822
RUN test -f /etc/apt/sources.list.d/debian.sources && \
    sed -i 's/deb.debian.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list.d/debian.sources ; \
    sed -i 's/security.debian.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list.d/debian.sources ; \
    exit 0
# <<< SPEEDUP APT zh-CN

# >>> SPEEDUP NPM
# Setup npm mirrors
RUN npm config set registry=https://registry.npmmirror.com
# <<< SPEEDUP NPM

# Prepare make python
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev \
    libreadline-dev libffi-dev libbz2-dev liblzma-dev libsqlite3-dev sqlite3 && \
    apt-get clean && \
    rm -rf /tmp/* && \
    rm -rf /var/lib/apt/lists/*

# Make python
ENV PYTHON_VERSION=3.13.2
RUN cd /tmp && \
    curl -SsL https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tar.xz -o - | \
    tar -Jxf - && mv Python-${PYTHON_VERSION} Python && cd Python && \
    ./configure --enable-optimizations && make

# Install python
RUN cd /tmp/Python && \
    make install && cd .. && \
    ln -s $(which python3) $(which python3 | sed -e s#python3#python#)

# Install pip
RUN curl -SsL https://bootstrap.pypa.io/get-pip.py | python && \
    python -m pip install --upgrade pip

# >>> SPEEDUP PIP
RUN pip config set --global global.index-url "https://mirrors.aliyun.com/pypi/simple"
## && pip config set global.extra-index-url "<url1> <url2>..." # extra index like pytorch 
# <<< SPEEDUP PIP

# Install pnpm
RUN wget -qO- https://get.pnpm.io/install.sh | ENV="$HOME/.shrc" SHELL="$(which sh)" sh -

# Install Next.JS
RUN npm install -g create-next-app