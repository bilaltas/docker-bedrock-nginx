#
# Bedrock Dependencies
#
FROM composer:2.2 as builder
COPY ./composer.json composer.json
COPY ./composer.lock composer.lock
RUN mkdir -p /app/web/app/mu-plugins
RUN mkdir -p /app/web/app/plugins
RUN mkdir -p /app/web/app/themes
RUN composer install --no-dev


#
# Application
#
FROM wordpress:php8.3-fpm-alpine

# Increase upload limit
COPY ./config/php/uploads.ini /usr/local/etc/php/conf.d/uploads.ini

# Install WPCLI and add Bedrock config
RUN curl -sS -o /usr/local/bin/wp https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar && \
	chmod +x /usr/local/bin/wp
COPY wp-cli.yml /project/wp-cli.yml

# Install NGINX + MySQL Client(For WPCLI) and add NGINX config
RUN apk update && apk add -f nginx mysql-client

# Validate NGINX configurations
RUN nginx -t

# Update NGINX temp folders permissions
RUN chown -R www-data:www-data /var/lib/nginx/ && \
    chown -R www-data:www-data /var/run/ && \
	chown -R www-data:www-data /etc/nginx/nginx.conf

# Forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log
RUN ln -sf /dev/stderr /var/log/nginx/error.log
RUN ln -sf /dev/stderr /var/log/nginx/debug.log

# Source files
ADD ./config /project/config
ADD ./web /project/web

# Add link for the NGINX config
RUN ln -sf /project/config/nginx/default.conf /etc/nginx/http.d/

# Bedrock built files
COPY --from=builder /app/vendor /project/vendor
COPY --from=builder /app/web/wp /project/web/wp
COPY --from=builder /app/web/app/mu-plugins/ /project/web/app/mu-plugins/
COPY --from=builder /app/web/app/plugins/ /project/web/app/plugins/
COPY --from=builder /app/web/app/themes/ /project/web/app/themes/

# Update the permissions
RUN chown -R www-data:www-data /project/ && \
    find /project/ -type f -exec chmod 644 {} \; && \
    find /project/ -type d -exec chmod 755 {} \;

# Project folder is working directory
WORKDIR /project

# Expose both secure & insecure
EXPOSE 80 443

# Run WP installation script, and PHP + NGINX
CMD php-fpm | nginx -g 'daemon off;'

# Switch to a safer user
USER www-data
