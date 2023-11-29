FROM php:8.2-apache-bullseye

# Surpresses debconf complaints of trying to install apt packages interactively
ARG DEBIAN_FRONTEND=noninteractive

# Update and install basic packages
RUN apt-get -y update --fix-missing && \
    apt-get upgrade -y && \
    apt-get --no-install-recommends install -y apt-utils && \
    rm -rf /var/lib/apt/lists/*

# Install essential packages
RUN apt-get -y update && \
    apt-get -y --no-install-recommends install \
    default-mysql-client \
    zlib1g-dev \
    libzip-dev \
    libicu-dev \
    libonig-dev \
    curl && \
    rm -rf /var/lib/apt/lists/*

# Install essential PHP extensions only
RUN docker-php-ext-install \
    pdo_mysql \
    mysqli \
    zip \
    intl \
    mbstring

# Enable Apache mod_rewrite
RUN a2enmod rewrite

# Set working directory
WORKDIR /var/www/html

# Expose port 80
EXPOSE 80
