FROM alpine:3.5
LABEL maintainer "Yovko Yovkov <yyovkov@yyovkov.net>"
LABEL Description "This image provides rsyslog server listening on port 514/tcp."

RUN apk --no-cache add rsyslog

COPY resources/rsyslog.conf /etc/rsyslog.conf

EXPOSE 514/tcp

VOLUME /var/log

ENTRYPOINT ["/usr/sbin/rsyslogd","-n","-i","/var/run/rsyslogd.pid"]
