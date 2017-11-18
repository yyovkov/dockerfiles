FROM alpine:3.5
LABEL maintainer.name "Yovko Yovkov"
LABEL maintainer.email "<yyovkov@yyovkov.net>"
LABEL Description "OpenVPN Server docker container"

env CONFIGDIR '/etc/openvpn'

RUN apk --no-cache add openvpn easy-rsa

RUN mkdir /etc/templates
COPY resources/templates/client.txt /etc/$CONFIGDIR/templates/client.conf
COPY resources/sbin/dockerrun /sbin/dockerrun

EXPOSE 1194/udp

VOLUME ["/etc/openvpn"]

CMD ["/sbin/dockerrun"]
