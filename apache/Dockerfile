FROM php:7.4-apache

ENV DEBIAN_FRONTEND=noninteractive

# Install system dependencies first
RUN apt-get update && apt-get install -y \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libzip-dev \
    libxml2-dev \
    libcurl4-openssl-dev \
    libonig-dev \
    openssl \
    nano \
    graphicsmagick \
    imagemagick \
    ghostscript \
    default-mysql-client \
    iputils-ping \
    locales \
    sqlite3 \
    ca-certificates \
    curl \
    unzip \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) \
    gd \
    zip \
    mbstring \
    xml \
    curl \
    bcmath \
    mysqli \
    pdo \
    pdo_mysql

# Install composer
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer \
    && chmod +x /usr/local/bin/composer

# Set locales
RUN sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen && \
    sed -i '/es_ES.UTF-8/s/^# //g' /etc/locale.gen && \
    sed -i '/fr_FR.UTF-8/s/^# //g' /etc/locale.gen && \
    sed -i '/de_DE.UTF-8/s/^# //g' /etc/locale.gen && \
    sed -i '/it_IT.UTF-8/s/^# //g' /etc/locale.gen && \
    locale-gen

# Configure apache
RUN a2enmod rewrite expires
RUN echo "ServerName localhost" | tee /etc/apache2/conf-available/servername.conf
RUN a2enconf servername

# Configurar PHP
RUN echo "memory_limit = 1024M" > /usr/local/etc/php/conf.d/custom.ini && \
    echo "max_execution_time = 3000" >> /usr/local/etc/php/conf.d/custom.ini && \
    echo "upload_max_filesize = 1024M" >> /usr/local/etc/php/conf.d/custom.ini && \
    echo "post_max_size = 512M" >> /usr/local/etc/php/conf.d/custom.ini

EXPOSE 80 443

WORKDIR /var/www/html

HEALTHCHECK --interval=5s --timeout=3s --retries=3 CMD curl -f http://localhost || exit 1

CMD ["apache2-foreground"]
