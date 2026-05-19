FROM python:3.11-slim-bookworm

WORKDIR /app

ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1
ENV TZ=Asia/Shanghai
ENV DOCKER_ENV=true
ENV PLAYWRIGHT_BROWSERS_PATH=/ms-playwright

RUN sed -i 's/deb.debian.org/mirrors.ustc.edu.cn/g' /etc/apt/sources.list.d/debian.sources

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        nodejs \
        npm \
        tzdata \
        curl \
        ca-certificates \
        libjpeg-dev \
        libpng-dev \
        libfreetype6-dev \
        fonts-dejavu-core \
        fonts-liberation \
        libnss3 \
        libnspr4 \
        libatk-bridge2.0-0 \
        libdrm2 \
        libxkbcommon0 \
        libxcomposite1 \
        libxdamage1 \
        libxrandr2 \
        libgbm1 \
        libxss1 \
        libasound2 \
        libatspi2.0-0 \
        libgtk-3-0 \
        libgdk-pixbuf2.0-0 \
        libxcursor1 \
        libxi6 \
        libxrender1 \
        libxext6 \
        libx11-6 \
        libxft2 \
        libxinerama1 \
        libxtst6 \
        libappindicator3-1 \
        libx11-xcb1 \
        libxfixes3 \
        xdg-utils \
        chromium \
        libgl1 \
        libglib2.0-0 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN node --version && npm --version

ENV NODE_PATH=/usr/lib/node_modules

COPY requirements.txt .

RUN python -V && \
    python -m pip install --no-cache-dir --upgrade pip setuptools wheel \
        -i https://mirrors.aliyun.com/pypi/simple/ \
        --trusted-host mirrors.aliyun.com && \
    python -m pip install --no-cache-dir -r requirements.txt \
        -i https://mirrors.aliyun.com/pypi/simple/ \
        --extra-index-url https://pypi.org/simple \
        --trusted-host mirrors.aliyun.com

COPY . .

RUN playwright install chromium

RUN mkdir -p /app/logs /app/data /app/backups /app/static/uploads/images && \
    chmod 777 /app/logs /app/data /app/backups /app/static/uploads /app/static/uploads/images

RUN echo "ulimit -c 0" >> /etc/profile

EXPOSE 8090

HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:8090/health || exit 1

COPY entrypoint.sh /app/entrypoint.sh
RUN chmod +x /app/entrypoint.sh

CMD ["/app/entrypoint.sh"]
