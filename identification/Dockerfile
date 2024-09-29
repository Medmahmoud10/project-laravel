FROM php:8.2-apache
RUN a2enmod rewrite headers deflate

# Install system dependencies
RUN apt-get update -y && apt-get install -y \
    supervisor \
    build-essential \
    git \
    curl \
    libjpeg-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libmcrypt-dev \
    libgd-dev \
    jpegoptim optipng pngquant gifsicle \
    libonig-dev \
    libxml2-dev \
    sudo \
    zip \
    unzip \
    npm \
    nodejs \
    libpng-dev \
    libzip-dev \
    libicu-dev \
    exiftool \
    libpq-dev \
    libmagickwand-dev --no-install-recommends

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

RUN pecl install imagick
RUN docker-php-ext-enable imagick

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN docker-php-ext-install sockets pdo pdo_mysql gd zip exif intl
RUN docker-php-ext-install -j$(nproc) gd
RUN docker-php-ext-configure exif
RUN docker-php-ext-enable exif
RUN docker-php-ext-configure gd --with-jpeg

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf


WORKDIR /var/www/html

COPY . ./
RUN composer update

RUN chown -R www-data:www-data /var/www/html/storage
RUN chown -R www-data:www-data /var/www/html/bootstrap

RUN chown -R www-data:www-data storage/framework/sessions
RUN chown -R www-data:www-data storage/framework/views
RUN chown -R www-data:www-data storage/logs
RUN chown -R www-data:www-data storage/app/public/
RUN chown -R 775 storage/framework/cache
RUN chown -R 775 storage/framework/cache/data
RUN chown -R 775 storage/app/public/


# RUN cp .env.develop .env
RUN composer dump-autoload --optimize

RUN php artisan key:generate
RUN php artisan config:clear
RUN php artisan cache:clear
RUN php artisan optimize:clear

RUN rm /etc/apache2/sites-available/000-default.conf && rm /etc/apache2/sites-enabled/000-default.conf
RUN cp vhost.docker.conf /etc/apache2/sites-available/vhost.docker.conf
RUN a2ensite vhost.docker.conf

EXPOSE 80

