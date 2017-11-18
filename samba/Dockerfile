FROM alpine:3.6

RUN apk --no-cache add samba-server

RUN mkdir /winshare

ADD config/smb.conf /etc/samba/smb.conf

EXPOSE 139 445

ENTRYPOINT ["/usr/sbin/smbd", "-FS"]
