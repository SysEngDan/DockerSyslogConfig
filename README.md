# DockerSyslogConfig
This repo will contain details as to how you can configure Docker services via a docker-compose formatted YAML file, to send all logs to a basic syslogger. Then, we'll configure rsyslog to collect and store the logs. Finally, we'll configure logrotated to handle the daily rollup of logs.

# Docker-Compose Configuration
```
    logging:
      driver: syslog
      options:
        tag: docker-service-name
        syslog-address: udp://192.168.125.9:514
```

# Rsyslog Configuration
```
# nano /etc/rsyslog.d/40-docker.conf

# Create a template for the target log file
$template CUSTOM_LOGS,"/data/logs/%programname%.log"

if $programname startswith  'docker-' then ?CUSTOM_LOGS
& STOP
```

# Logrotate Configuration
```
# nano /etc/logrotate.d/docker-logs

/data/logs/*.log {
   daily
   rotate 20
   missingok
   compress
   notifyempty
   postrotate
        service rsyslog force-reload >/dev/null 2>&1 || true
   endscript
}
```

# Folder Setup

```
mkdir -p /data/logs
```
