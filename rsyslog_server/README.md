# syslog_server

## Build Docker Image
```bash
$ docker build -t yyovkov/rsyslog_server .
```

## Run service
```bash
$ docker run -dt --name rsyslog_server -p 514:514 -v /var/rsyslog_server:/var/log yyovkov/rsyslog_server
```

## To configure rsyslog node:
```bash
$ echo -e '*.*\t@@logserer.tld:514' > /etc/rsyslog.d/10_remote_logging.conf
```
