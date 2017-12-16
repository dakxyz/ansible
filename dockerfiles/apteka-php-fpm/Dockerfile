FROM php:7.1-fpm

MAINTAINER Denis Akulov <akulov.d.g@gmail.com>

ENV PHALCON_VERSION=3.2.4

RUN curl -sSL "https://codeload.github.com/phalcon/cphalcon/tar.gz/v${PHALCON_VERSION}" | tar -xz \
    && cd cphalcon-${PHALCON_VERSION}/build \
    && ./install \
    && cp ../tests/_ci/phalcon.ini $(php-config --configure-options | grep -o "with-config-file-scan-dir=\([^ ]*\)" | awk -F'=' '{print $2}') \
    && cd ../../ \
    && rm -r cphalcon-${PHALCON_VERSION}

RUN cd /tmp && curl https://codeload.github.com/phalcon/phalcon-devtools/tar.gz/v3.2.11 -o phalcon-devtools.tar.gz \
    && tar -xzf phalcon-devtools.tar.gz \
    && mv phalcon-devtools-3.2.11 /opt/ \
    && ln -s /opt/phalcon-devtools-3.2.11/phalcon.php /usr/local/bin/phalcon \
    && chmod +x /usr/local/bin/phalcon

RUN cd /tmp \
    && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
    && php composer-setup.php --install-dir=/bin --filename=composer  --version=1.5.5 \
    && php -r "unlink('composer-setup.php');"

RUN cd /tmp \
    && apt-get update && apt-get install -y make autoconf automake libtool \
    && curl https://codeload.github.com/yaml/libyaml/tar.gz/0.1.7 -o libyaml.tar.gz \
    && tar -xzf libyaml.tar.gz \
    && cd libyaml-0.1.7 \
    && ./bootstrap \
    && ./configure \
    && make "-j$(nproc)" \
    && make install \
    && pecl install yaml \
    && touch /usr/local/etc/php/conf.d/yaml.ini \
    && echo '[yaml]' > /usr/local/etc/php/conf.d/yaml.ini \
    && echo 'extension=yaml.so' >> /usr/local/etc/php/conf.d/yaml.ini


WORKDIR /app