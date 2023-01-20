ARG DEBIAN_VERSION=bullseye
FROM debian:${DEBIAN_VERSION} AS build

ARG OPENLDAP_VERSION=2.6.3

RUN apt-get update -qq \
    && apt-get install -y openssl libsasl2-dev libevent-dev libsodium-dev \
    make gcc curl libdigest-sha3-perl groff-base

WORKDIR /build

RUN curl -o openldap.tgz "https://www.openldap.org/software/download/OpenLDAP/openldap-release/openldap-${OPENLDAP_VERSION}.tgz" \
    && curl -o openldap.sha3-512 "https://www.openldap.org/software/download/OpenLDAP/openldap-release/openldap-${OPENLDAP_VERSION}.sha3-512" \
    && export CHECKSUM=$(cut -d" " -f2 < openldap.sha3-512) && echo "$CHECKSUM  openldap.tgz" | sha3sum -a 512 -c - \
    && tar -xzf openldap.tgz \
    && cd openldap-${OPENLDAP_VERSION} \
    && ./configure --prefix=/usr/local/ \
    && make depend \
    && make \
    && make install

FROM debian:${DEBIAN_VERSION}

ARG LIBSODIUM_VERSION=23
ARG LIBEVENT_VERSION=2.1.7

RUN apt-get update -qq \
    && apt-get install -y openssl libsasl2-2 libsasl2-modules-gssapi-mit krb5-user libgssapi-krb5-2 \
    libsasl2-modules-ldap libsodium${LIBSODIUM_VERSION} libevent-${LIBEVENT_VERSION}

COPY --from=build /usr/local/bin/ldap* /usr/local/bin/
RUN chmod +x /usr/local/bin/ldap*
