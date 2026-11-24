FROM php:8.5-fpm

# копируем официальный node в изолированный каталог
COPY --from=node:22-bookworm /usr/local /opt/node

# дописываем путь к исполняемым файлам в переменную окружения
ENV PATH="/opt/node/bin:${PATH}"

# устанавливаем необходимые зависимости
RUN apt-get update && apt-get install -y \
    libzip-dev \
    zip \
    unzip \
    curl \
    git \
    libpq-dev \
    postgresql-client \
    libicu-dev

# - libzip-dev: Библиотека для работы с ZIP-архивами (требуется для расширения zip)
# - zip: Утилита для работы с ZIP-архивами
# - unzip: Утилита для распаковки ZIP-архивов
# - curl: Утилита для выполнения HTTP-запросов
# - git: Система контроля версий
# - libpq-dev: Библиотеки и заголовочные файлы для PostgreSQL (требуется для pdo_pgsql)
# - postgresql-client: Клиентские утилиты для PostgreSQL (для отладки и администрирования)
# - libicu-dev: Библиотека для интернационализации и Unicode (требуется для intl)

# расширения PHP
RUN docker-php-ext-install \
    zip \
    intl \
    pdo \
    pdo_pgsql \
    sockets

# - zip: Расширение для работы с ZIP-архивами
# - intl: Расширение для интернационализации (форматирование дат, чисел, текста)
# - pdo: Расширение PDO (PHP Data Objects) для работы с базами данных
# - pdo_pgsql: Драйвер PDO для PostgreSQL
# - sockets: Расширение для работы с RabbitMQ

# установка Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && chmod +x /usr/local/bin/composer

# Xdebug
RUN pecl install xdebug && docker-php-ext-enable xdebug;

# настройка non-root user
ARG PUID
ARG PGID
RUN groupmod -o -g ${PGID} www-data && \
    usermod -o -u ${PUID} -g www-data www-data

RUN chown www-data:www-data /var/www/html

ARG PHP_FPM_PORT
ARG NODE_PORT
EXPOSE ${PHP_FPM_PORT} ${NODE_PORT}

CMD ["php-fpm"]
