# Use a multi-stage build to reduce the final image size

# Stage 1: Build the PHP application (install extensions, copy source)
FROM php:8.2-apache AS builder

# Install necessary extensions (mysqli, and any others you need)
RUN apt-get update && apt-get install -y libpng-dev zlib1g-dev \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/freetype2 \
    && docker-php-ext-install -j$(awk '/MemTotal:/ {print int($2/1024) "G"}' /proc/meminfo) gd pdo pdo_mysql mysqli

# Copy your application code
COPY . /var/www/html/

# Stage 2: Create a smaller, more efficient image for deployment
FROM php:8.2-apache

# Copy only the necessary files from the builder stage
COPY --from=builder /var/www/html /var/www/html

# Expose port 80 (Apache's default)
EXPOSE 80

# Set the working directory inside the container
WORKDIR /var/www/html

# If you need to set any specific PHP configurations, you can do it here
# For example, for error reporting:
# RUN echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/custom.ini

# Start Apache
CMD ["apache2-foreground"] # This is usually the default, but explicit is good
