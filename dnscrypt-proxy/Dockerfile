FROM alpine:latest

ARG VERSION=2.0.39
ARG ARCH=arm
ARG DCP=dnscrypt-proxy

RUN apk add --update bind-tools curl libcap && \
	rm -rf /var/cache/apk/*

RUN echo $(uname -a)

RUN curl -sL https://github.com/jedisct1/${DCP}/releases/download/${VERSION}/${DCP}-linux_${ARCH}-${VERSION}.tar.gz |\
	tar -xzf - && \
	mv linux-${ARCH} /etc/${DCP} && \
	cp /etc/${DCP}/example-${DCP}.toml /etc/${DCP}/${DCP}.toml && \
	mv /etc/${DCP}/${DCP} /usr/local/bin && \
	setcap 'cap_net_bind_service=+ep' /usr/local/bin/${DCP}

RUN mkdir -p /var/run/${DCP} && \
	addgroup -g 1000 dnscrypt && \
	adduser -SHD -u 1000 -G dnscrypt dnscrypt && \
	chown -R dnscrypt:dnscrypt /etc/${DCP} /var/run/${DCP}

USER dnscrypt

ENTRYPOINT ["dnscrypt-proxy", \
	"-config", "/etc/dnscrypt-proxy/dnscrypt-proxy.toml", \
	"-pidfile", "/var/run/dnscrypt-proxy/dnscrypt-proxy.pid", \
	"-syslog" \
]

EXPOSE 5300

HEALTHCHECK --interval=10s --timeout=3s --retries=3 CMD dig one.one.one.one || exit 1
