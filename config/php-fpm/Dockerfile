FROM php:8.4-fpm-alpine

WORKDIR /var/www

# Set timezone
ENV TZ=Asia/Dhaka

RUN apk add --no-cache tzdata \
    && cp /usr/share/zoneinfo/${TZ} /etc/localtime \
    && echo "${TZ}" > /etc/timezone

# Runtime dependencies
RUN apk add --no-cache \
    bash \
    git \
    curl \
    wget \
    unzip \
    zip \
    imagemagick \
    openssl \
    libpng \
    libjpeg-turbo \
    freetype \
    postgresql-libs \
    libzip \
    libxslt \
    icu \
    libxml2 \
    gettext \
    mysql-client \
    rabbitmq-c

# Build dependencies + PHP extensions
RUN apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        build-base \
        linux-headers \
        autoconf \
        imagemagick-dev \
        openssl-dev \
        libpng-dev \
        libjpeg-turbo-dev \
        freetype-dev \
        postgresql-dev \
        libzip-dev \
        libxslt-dev \
        icu-dev \
        zlib-dev \
        mariadb-connector-c-dev \
        libxml2-dev \
        oniguruma-dev \
        rabbitmq-c-dev \
    \
    && docker-php-ext-configure gd \
        --with-freetype \
        --with-jpeg \
    \
    && docker-php-ext-install \
        gd \
        mysqli \
        mbstring \
        pdo \
        pdo_mysql \
        pdo_pgsql \
        xml \
        pcntl \
        bcmath \
        zip \
        intl \
        soap \
        sockets \
        xsl \
    \
    && pecl channel-update pecl.php.net \
    \
    && pecl install imagick \
    && docker-php-ext-enable imagick \
    \
    && pecl install redis \
    && docker-php-ext-enable redis \
    \
    && pecl install amqp \
    && docker-php-ext-enable amqp \
    \
    && apk del .build-deps \
    && rm -rf /tmp/* /var/cache/apk/*

# Install Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Recommended for Laravel
ENV COMPOSER_ALLOW_SUPERUSER=1

EXPOSE 9000

CMD ["php-fpm"]