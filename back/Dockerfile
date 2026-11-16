FROM dunglas/frankenphp:1-php8.5

WORKDIR /app

# Extensions PHP : Composer, cache, internationalisation, cache d'opcodes, PostgreSQL, archives
RUN install-php-extensions @composer apcu intl opcache pdo_pgsql zip

# Réglages PHP de développement (erreurs affichées)
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

# Configuration du serveur web
COPY frankenphp/Caddyfile /etc/frankenphp/Caddyfile

CMD ["frankenphp", "run", "--config", "/etc/frankenphp/Caddyfile"]
